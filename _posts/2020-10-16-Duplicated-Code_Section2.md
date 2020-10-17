Case study 1:
The following two methods are duplicated code, they reside in two different classes.

Method 1:
```java
public int read() throws IOException {
        if (!getInitialized()) {
            initialize();
            setInitialized(true);
        }

        int ch = -1;

        if (line != null) {
            ch = line.charAt(0);
            if (line.length() == 1) {
                line = null;
            } else {
                line = line.substring(1);
            }
        } else {
            final int regexpsSize = regexps.size();

            for (line = readLine(); line != null; line = readLine()) {
                boolean matches = true;
                for (int i = 0; matches && i < regexpsSize; i++) {
                    RegularExpression regexp
                        = (RegularExpression) regexps.elementAt(i);
                    Regexp re = regexp.getRegexp(getProject());
                    matches = re.matches(line);
                }
                if (matches ^ isNegated()) {
                    break;
                }
            }
            if (line != null) {
                return read();
            }
        }
        return ch;
    }
```
Method 2:

```java
public int read() throws IOException {
        if (!getInitialized()) {
            initialize();
            setInitialized(true);
        }

        int ch = -1;

        if (line != null) {
            ch = line.charAt(0);
            if (line.length() == 1) {
                line = null;
            } else {
                line = line.substring(1);
            }
        } else {
            final int containsSize = contains.size();

            for (line = readLine(); line != null; line = readLine()) {
                boolean matches = true;
                for (int i = 0; matches && i < containsSize; i++) {
                    String containsStr = (String) contains.elementAt(i);
                    matches = line.indexOf(containsStr) >= 0;
                }
                if (matches ^ isNegated()) {
                    break;
                }
            }
            if (line != null) {
                return read();
            }
        }
        return ch;
    }
```
The differences are:

![alt](https://i.imgur.com/UwT24qS.png)

1. Method 1 is using a filed: contains, Method 2 is using a filed: regexp.
2. They are both calculating a boolean: match.
3. The differences have two parts, but actually the first part can be moved to second part, inline it. So we have a whole one differences partition.
```java
for (line = readLine(); line != null; line = readLine()) {
                boolean matches = true;
                for (int i = 0; matches && i < contains.size(); i++) {
                    String containsStr = (String) contains.elementAt(i);
                    matches = line.indexOf(containsStr) >= 0;
                }
                if (matches ^ isNegated()) {
                    break;
                }
            }
```
For this different part, first we extract a method for both.
```java
private boolean matches() {
		boolean matches = true;
		for (int i = 0; matches && i < contains.size(); i++) {
		    String containsStr = (String) contains.elementAt(i);
		    matches = line.indexOf(containsStr) >= 0;
		}
		return matches;
	}
```
and 
```java
private boolean matches() {
		boolean matches = true;
		for (int i = 0; matches && i < regexps.size(); i++) {
		    RegularExpression regexp
		        = (RegularExpression) regexps.elementAt(i);
		    Regexp re = regexp.getRegexp(getProject());
		    matches = re.matches(line);
		}
		return matches;
	}
```
Now the `read()` are the same.

Because the two classes have a common super class, so we can move function `read()` to super class:

```java
protected int readExtracted() throws IOException {
		if (!getInitialized()) {
			initialize();
			setInitialized(true);
		}
		int ch = -1;
		if (line != null) {
			ch = line.charAt(0);
			if (line.length() == 1) {
				line = null;
			} else {
				line = line.substring(1);
			}
		} else {
			for (line = readLine(); line != null; line = readLine()) {
				boolean matches = matches();
				if (matches ^ isNegated()) {
					break;
				}
			}
			if (line != null) {
				return read();
			}
		}
		return ch;
	}
```

But please note that there are some other methods are called in `read()`:

```java
getInitialized();
initialize();
setInitialized(true);
```
And also, we need check if there are any other method calls in above three methods. Hopefully, all the other methods called reside superclass already.

So we need to check if called methods are the same or different. If they are the same, we can move it to super class as well, if not, we must declare abstract function in super class.
And, if the two class have the same fields, we can also move the fields to super class.

For this example, we pull up two fields, three methods, and two abstract methods:

```java
protected String line = null;
protected boolean negate = false;

public boolean isNegated() {
		return negate;
}

public void setNegate(boolean b) {
		negate = b;
}

protected int readExtracted() throws IOException {
		if (!getInitialized()) {
			initialize();
			setInitialized(true);
		}
		int ch = -1;
		if (line != null) {
			ch = line.charAt(0);
			if (line.length() == 1) {
				line = null;
			} else {
				line = line.substring(1);
			}
		} else {
			for (line = readLine(); line != null; line = readLine()) {
				boolean matches = matches();
				if (matches ^ isNegated()) {
					break;
				}
			}
			if (line != null) {
				return read();
			}
		}
		return ch;
}

protected abstract void initialize();
protected abstract boolean matches();

```
Above is using template method design pattern, next we can use lambda expression.

Literally, we are removing two abstract methods by using lambda expression.

Two abstract methods are `initialize()` and `matches()`.

For `initialize()`, we are doing something without return, so we can use a `Consumer` to call `initialize()` in subclass.
For `matches()`, we are returning a boolean, so we can have a `Supplier`.

```java
protected int read(Consumer<Parameter> initializer, Supplier<Boolean> matcher) throws IOException {
    if (!getInitialized()) {
			Parameter[] params = getParameters();
			if (params != null) {				
			    for (int i = 0; i < params.length; i++) {				    for (int i = 0; i < params.length; i++) {
			        	initializer.accept(parameter);//functional interface
                        }
			    }		
			}
			setInitialized(true);
		}
		int ch = -1;
		if (line != null) {
			ch = line.charAt(0);
			if (line.length() == 1) {
				line = null;
			} else {
				line = line.substring(1);
			}
		} else {
			for (line = readLine(); line != null; line = readLine()) {
				boolean matches = matcher.get();//functional interface
				if (matches ^ isNegated()) {
					break;
				}
			}
			if (line != null) {
				return read();
			}
		}
		return ch;
}
```
Note that we have extracted some common code of `initialize()` method of each subclass.

In subclasses:

```java
public int read() throws IOException {
    	return read((Parameter p) -> initialize(p), () -> match());
    }
```

Case study 2:

Two methods are duplicated code, residing in two classes.

Method 1:
```java
public LegendItem getLegendItem(int datasetIndex, int series) {

        CategoryPlot cp = getPlot();
        if (cp == null) {
            return null;
        }

        if (isSeriesVisible(series) && isSeriesVisibleInLegend(series)) {
            CategoryDataset dataset = cp.getDataset(datasetIndex);
            String label = getLegendItemLabelGenerator().generateLabel(
                    dataset, series);
            String description = label;
            String toolTipText = null;
            if (getLegendItemToolTipGenerator() != null) {
                toolTipText = getLegendItemToolTipGenerator().generateLabel(
                        dataset, series);
            }
            String urlText = null;
            if (getLegendItemURLGenerator() != null) {
                urlText = getLegendItemURLGenerator().generateLabel(
                        dataset, series);
            }
            Shape shape = lookupLegendShape(series);
            Paint paint = lookupSeriesPaint(series);
            Paint fillPaint = (this.useFillPaint
                    ? getItemFillPaint(series, 0) : paint);
            boolean shapeOutlineVisible = this.drawOutlines;
            Paint outlinePaint = (this.useOutlinePaint
                    ? getItemOutlinePaint(series, 0) : paint);
            Stroke outlineStroke = lookupSeriesOutlineStroke(series);
            LegendItem result = new LegendItem(label, description, toolTipText,
                    urlText, true, shape, getItemShapeFilled(series, 0),
                    fillPaint, shapeOutlineVisible, outlinePaint, outlineStroke,
                    false, new Line2D.Double(-7.0, 0.0, 7.0, 0.0),
                    getItemStroke(series, 0), getItemPaint(series, 0));
            result.setLabelFont(lookupLegendTextFont(series));
            Paint labelPaint = lookupLegendTextPaint(series);
            if (labelPaint != null) {
                result.setLabelPaint(labelPaint);
            }
            result.setDataset(dataset);
            result.setDatasetIndex(datasetIndex);
            result.setSeriesKey(dataset.getRowKey(series));
            result.setSeriesIndex(series);
            return result;
        }
        return null;

    }
```

Method 2:
```java
public LegendItem getLegendItem(int datasetIndex, int series) {

        CategoryPlot cp = getPlot();
        if (cp == null) {
            return null;
        }

        if (isSeriesVisible(series) && isSeriesVisibleInLegend(series)) {
            CategoryDataset dataset = cp.getDataset(datasetIndex);
            String label = getLegendItemLabelGenerator().generateLabel(
                    dataset, series);
            String description = label;
            String toolTipText = null;
            if (getLegendItemToolTipGenerator() != null) {
                toolTipText = getLegendItemToolTipGenerator().generateLabel(
                        dataset, series);
            }
            String urlText = null;
            if (getLegendItemURLGenerator() != null) {
                urlText = getLegendItemURLGenerator().generateLabel(
                        dataset, series);
            }
            Shape shape = lookupLegendShape(series);
            Paint paint = lookupSeriesPaint(series);
            Paint fillPaint = (this.useFillPaint
                    ? getItemFillPaint(series, 0) : paint);
            boolean shapeOutlineVisible = this.drawOutlines;
            Paint outlinePaint = (this.useOutlinePaint
                    ? getItemOutlinePaint(series, 0) : paint);
            Stroke outlineStroke = lookupSeriesOutlineStroke(series);
            boolean lineVisible = getItemLineVisible(series, 0);
            boolean shapeVisible = getItemShapeVisible(series, 0);
            LegendItem result = new LegendItem(label, description, toolTipText,
                    urlText, shapeVisible, shape, getItemShapeFilled(series, 0),
                    fillPaint, shapeOutlineVisible, outlinePaint, outlineStroke,
                    lineVisible, new Line2D.Double(-7.0, 0.0, 7.0, 0.0),
                    getItemStroke(series, 0), getItemPaint(series, 0));
            result.setLabelFont(lookupLegendTextFont(series));
            Paint labelPaint = lookupLegendTextPaint(series);
            if (labelPaint != null) {
                result.setLabelPaint(labelPaint);
            }
            result.setDataset(dataset);
            result.setDatasetIndex(datasetIndex);
            result.setSeriesKey(dataset.getRowKey(series));
            result.setSeriesIndex(series);
            return result;
        }
        return null;

    }
```
The differences:

![alt](https://i.imgur.com/cSqWOwW.png)

All methods that being called in `getLegendItem()` are from super class except the methods:
`getItemLineVisible()`, `getItemShapeVisible()`

1. Inline above to methods to `new LegendItem()`, so now the difference is only the parameters of new object.
2. The parameters' differences are the two methods that we inlined. So we can use two `Supplier` to provide the correct value.
3. `getItemShapeFilled()` and it is calling `getSeriesShapesFilled()`, these two methods are identical, we can move them directly to super class.

Pull up the common part to super class:
```java
//src/duplicatedCode/jfreechart/getLegendItem/AbstractCategoryItemRenderer.java
protected LegendItem getLegendItemExtracted(int series, int datasetIndex, Supplier<Boolean> arg0, Supplier<Boolean> arg1, boolean thisUseFillPaint, boolean thisDrawOutlines, boolean thisUseOutlinePaint) {
		CategoryPlot cp = getPlot();
		if (cp == null) {
			return null;
		}
		if (isSeriesVisible(series) && isSeriesVisibleInLegend(series)) {
			CategoryDataset dataset = cp.getDataset(datasetIndex);
			String label = getLegendItemLabelGenerator().generateLabel(dataset, series);
			String description = label;
			String toolTipText = null;
			if (getLegendItemToolTipGenerator() != null) {
				toolTipText = getLegendItemToolTipGenerator().generateLabel(dataset, series);
			}
			String urlText = null;
			if (getLegendItemURLGenerator() != null) {
				urlText = getLegendItemURLGenerator().generateLabel(dataset, series);
			}
			Shape shape = lookupLegendShape(series);
			Paint paint = lookupSeriesPaint(series);
			Paint fillPaint = (thisUseFillPaint ? getItemFillPaint(series, 0) : paint);
			boolean shapeOutlineVisible = thisDrawOutlines;
			Paint outlinePaint = (thisUseOutlinePaint ? getItemOutlinePaint(series, 0) : paint);
			Stroke outlineStroke = lookupSeriesOutlineStroke(series);
			LegendItem result = new LegendItem(label, description, toolTipText, urlText, (boolean) arg0.get(), shape,
					getItemShapeFilled(series, 0), fillPaint, shapeOutlineVisible, outlinePaint, outlineStroke,
					(boolean) arg1.get(), new Line2D.Double(-7.0, 0.0, 7.0, 0.0), getItemStroke(series, 0),
					getItemPaint(series, 0));
			result.setLabelFont(lookupLegendTextFont(series));
			Paint labelPaint = lookupLegendTextPaint(series);
			if (labelPaint != null) {
				result.setLabelPaint(labelPaint);
			}
			result.setDataset(dataset);
			result.setDatasetIndex(datasetIndex);
			result.setSeriesKey(dataset.getRowKey(series));
			result.setSeriesIndex(series);
			return result;
		}
		return null;
	}
```
In subclasses:

```java
public LegendItem getLegendItem(int datasetIndex, int series) {
 return getLegendItemExtracted(series, datasetIndex, () -> getItemShapeVisible(series, 0), () -> getItemLineVisible(series, 0), this.useFillPaint, this.drawOutlines, this.useOutlinePaint);
```
And
```java
public LegendItem getLegendItem(int datasetIndex, int series) {

return getLegendItemExtracted(series, datasetIndex, () -> true, () -> false, this.useFillPaint, this.drawOutlines, this.useOutlinePaint);
```

Case study 3:

Duplicated methods in same class:

Method 1:

```java
protected void drawStackHorizontal(List values, Comparable category,
            Graphics2D g2, CategoryItemRendererState state,
            Rectangle2D dataArea, CategoryPlot plot,
            CategoryAxis domainAxis, ValueAxis rangeAxis,
            CategoryDataset dataset) {

        int column = dataset.getColumnIndex(category);
        double barX0 = domainAxis.getCategoryMiddle(column,
                dataset.getColumnCount(), dataArea, plot.getDomainAxisEdge())
                - state.getBarWidth() / 2.0;
        double barW = state.getBarWidth();

        // a list to store the series index and bar region, so we can draw
        // all the labels at the end...
        List itemLabelList = new ArrayList();

        // draw the blocks
        boolean inverted = rangeAxis.isInverted();
        int blockCount = values.size() - 1;
        for (int k = 0; k < blockCount; k++) {
            int index = (inverted ? blockCount - k - 1 : k);
            Object[] prev = (Object[]) values.get(index);
            Object[] curr = (Object[]) values.get(index + 1);
            int series = 0;
            if (curr[0] == null) {
                series = -((Integer) prev[0]).intValue() - 1;
            }
            else {
                series = ((Integer) curr[0]).intValue();
                if (series < 0) {
                    series = -((Integer) prev[0]).intValue() - 1;
                }
            }
            double v0 = ((Double) prev[1]).doubleValue();
            double vv0 = rangeAxis.valueToJava2D(v0, dataArea,
                    plot.getRangeAxisEdge());

            double v1 = ((Double) curr[1]).doubleValue();
            double vv1 = rangeAxis.valueToJava2D(v1, dataArea,
                    plot.getRangeAxisEdge());

            Shape[] faces = createHorizontalBlock(barX0, barW, vv0, vv1,
                    inverted);
            Paint fillPaint = getItemPaint(series, column);
            Paint fillPaintDark = fillPaint;
            if (fillPaintDark instanceof Color) {
                fillPaintDark = ((Color) fillPaint).darker();
            }
            boolean drawOutlines = isDrawBarOutline();
            Paint outlinePaint = fillPaint;
            if (drawOutlines) {
                outlinePaint = getItemOutlinePaint(series, column);
                g2.setStroke(getItemOutlineStroke(series, column));
            }
            for (int f = 0; f < 6; f++) {
                if (f == 5) {
                    g2.setPaint(fillPaint);
                }
                else {
                    g2.setPaint(fillPaintDark);
                }
                g2.fill(faces[f]);
                if (drawOutlines) {
                    g2.setPaint(outlinePaint);
                    g2.draw(faces[f]);
                }
            }

            itemLabelList.add(new Object[] {new Integer(series),
                    faces[5].getBounds2D(),
                    BooleanUtilities.valueOf(v0 < getBase())});

            // add an item entity, if this information is being collected
            EntityCollection entities = state.getEntityCollection();
            if (entities != null) {
                addItemEntity(entities, dataset, series, column, faces[5]);
            }

        }

        for (int i = 0; i < itemLabelList.size(); i++) {
            Object[] record = (Object[]) itemLabelList.get(i);
            int series = ((Integer) record[0]).intValue();
            Rectangle2D bar = (Rectangle2D) record[1];
            boolean neg = ((Boolean) record[2]).booleanValue();
            CategoryItemLabelGenerator generator
                    = getItemLabelGenerator(series, column);
            if (generator != null && isItemLabelVisible(series, column)) {
                drawItemLabel(g2, dataset, series, column, plot, generator,
                        bar, neg);
            }

        }
    }
```
Method 2:

```java
protected void drawStackVertical(List values, Comparable category,
            Graphics2D g2, CategoryItemRendererState state,
            Rectangle2D dataArea, CategoryPlot plot,
            CategoryAxis domainAxis, ValueAxis rangeAxis,
            CategoryDataset dataset) {

        int column = dataset.getColumnIndex(category);
        double barX0 = domainAxis.getCategoryMiddle(column,
                dataset.getColumnCount(), dataArea, plot.getDomainAxisEdge())
                - state.getBarWidth() / 2.0;
        double barW = state.getBarWidth();

        // a list to store the series index and bar region, so we can draw
        // all the labels at the end...
        List itemLabelList = new ArrayList();

        // draw the blocks
        boolean inverted = rangeAxis.isInverted();
        int blockCount = values.size() - 1;
        for (int k = 0; k < blockCount; k++) {
            int index = (inverted ? blockCount - k - 1 : k);
            Object[] prev = (Object[]) values.get(index);
            Object[] curr = (Object[]) values.get(index + 1);
            int series = 0;
            if (curr[0] == null) {
                series = -((Integer) prev[0]).intValue() - 1;
            }
            else {
                series = ((Integer) curr[0]).intValue();
                if (series < 0) {
                    series = -((Integer) prev[0]).intValue() - 1;
                }
            }
            double v0 = ((Double) prev[1]).doubleValue();
            double vv0 = rangeAxis.valueToJava2D(v0, dataArea,
                    plot.getRangeAxisEdge());

            double v1 = ((Double) curr[1]).doubleValue();
            double vv1 = rangeAxis.valueToJava2D(v1, dataArea,
                    plot.getRangeAxisEdge());

            Shape[] faces = createVerticalBlock(barX0, barW, vv0, vv1,
                    inverted);
            Paint fillPaint = getItemPaint(series, column);
            Paint fillPaintDark = fillPaint;
            if (fillPaintDark instanceof Color) {
                fillPaintDark = ((Color) fillPaint).darker();
            }
            boolean drawOutlines = isDrawBarOutline();
            Paint outlinePaint = fillPaint;
            if (drawOutlines) {
                outlinePaint = getItemOutlinePaint(series, column);
                g2.setStroke(getItemOutlineStroke(series, column));
            }

            for (int f = 0; f < 6; f++) {
                if (f == 5) {
                    g2.setPaint(fillPaint);
                }
                else {
                    g2.setPaint(fillPaintDark);
                }
                g2.fill(faces[f]);
                if (drawOutlines) {
                    g2.setPaint(outlinePaint);
                    g2.draw(faces[f]);
                }
            }

            itemLabelList.add(new Object[] {new Integer(series),
                    faces[5].getBounds2D(),
                    BooleanUtilities.valueOf(v0 < getBase())});

            // add an item entity, if this information is being collected
            EntityCollection entities = state.getEntityCollection();
            if (entities != null) {
                addItemEntity(entities, dataset, series, column, faces[5]);
            }

        }

        for (int i = 0; i < itemLabelList.size(); i++) {
            Object[] record = (Object[]) itemLabelList.get(i);
            int series = ((Integer) record[0]).intValue();
            Rectangle2D bar = (Rectangle2D) record[1];
            boolean neg = ((Boolean) record[2]).booleanValue();
            CategoryItemLabelGenerator generator
                    = getItemLabelGenerator(series, column);
            if (generator != null && isItemLabelVisible(series, column)) {
                drawItemLabel(g2, dataset, series, column, plot, generator,
                        bar, neg);
            }

        }
    }
```
The difference:
![alt](https://i.imgur.com/0sfhCYw.png)

Only one method is different.

So we can use lambda expression, but because it's require returning a `Shape[]` and it has many parameters, so we cannot use `Supplier` or `Consumer` or `Function`, so we can create a functional interface.

```java
private interface Interface0 {
		Shape[] apply(double barX0, double barW, double vv0, double vv1, boolean inverted);
	}
```

The extracted method:

```java
private void drawStack(Comparable category, CategoryDataset dataset, CategoryItemRendererState state,
			Rectangle2D dataArea, CategoryPlot plot, CategoryAxis domainAxis, ValueAxis rangeAxis, List values,
			Graphics2D g2, Interface0 arg0) {
		int column = dataset.getColumnIndex(category);
		double barX0 = domainAxis.getCategoryMiddle(column, dataset.getColumnCount(), dataArea,
				plot.getDomainAxisEdge()) - state.getBarWidth() / 2.0;
		double barW = state.getBarWidth();
		List itemLabelList = new ArrayList();
		boolean inverted = rangeAxis.isInverted();
		int blockCount = values.size() - 1;
		for (int k = 0; k < blockCount; k++) {
			int index = (inverted ? blockCount - k - 1 : k);
			Object[] prev = (Object[]) values.get(index);
			Object[] curr = (Object[]) values.get(index + 1);
			int series = 0;
			if (curr[0] == null) {
				series = -((Integer) prev[0]).intValue() - 1;
			} else {
				series = ((Integer) curr[0]).intValue();
				if (series < 0) {
					series = -((Integer) prev[0]).intValue() - 1;
				}
			}
			double v0 = ((Double) prev[1]).doubleValue();
			double vv0 = rangeAxis.valueToJava2D(v0, dataArea, plot.getRangeAxisEdge());
			double v1 = ((Double) curr[1]).doubleValue();
			double vv1 = rangeAxis.valueToJava2D(v1, dataArea, plot.getRangeAxisEdge());
			Shape[] faces = arg0.apply(barX0, barW, vv0, vv1, inverted);
			Paint fillPaint = getItemPaint(series, column);
			Paint fillPaintDark = fillPaint;
			if (fillPaintDark instanceof Color) {
				fillPaintDark = ((Color) fillPaint).darker();
			}
			boolean drawOutlines = isDrawBarOutline();
			Paint outlinePaint = fillPaint;
			if (drawOutlines) {
				outlinePaint = getItemOutlinePaint(series, column);
				g2.setStroke(getItemOutlineStroke(series, column));
			}
			for (int f = 0; f < 6; f++) {
				if (f == 5) {
					g2.setPaint(fillPaint);
				} else {
					g2.setPaint(fillPaintDark);
				}
				g2.fill(faces[f]);
				if (drawOutlines) {
					g2.setPaint(outlinePaint);
					g2.draw(faces[f]);
				}
			}
			itemLabelList.add(new Object[] { new Integer(series), faces[5].getBounds2D(),
					BooleanUtilities.valueOf(v0 < getBase()) });
			EntityCollection entities = state.getEntityCollection();
			if (entities != null) {
				addItemEntity(entities, dataset, series, column, faces[5]);
			}
		}
		for (int i = 0; i < itemLabelList.size(); i++) {
			Object[] record = (Object[]) itemLabelList.get(i);
			int series = ((Integer) record[0]).intValue();
			Rectangle2D bar = (Rectangle2D) record[1];
			boolean neg = ((Boolean) record[2]).booleanValue();
			CategoryItemLabelGenerator generator = getItemLabelGenerator(series, column);
			if (generator != null && isItemLabelVisible(series, column)) {
				drawItemLabel(g2, dataset, series, column, plot, generator, bar, neg);
			}
		}
		return;
	}
```
Call extracted method using lambda expression:
```java
protected void drawStackHorizontal(List values, Comparable category,
            Graphics2D g2, CategoryItemRendererState state,
            Rectangle2D dataArea, CategoryPlot plot,
            CategoryAxis domainAxis, ValueAxis rangeAxis,
            CategoryDataset dataset) {

            drawStack(category, dataset, state, dataArea, plot, domainAxis, rangeAxis, values, g2, (double barX0, double barW, double vv0, double vv1, boolean inverted) -> createVerticalBlock(barX0, barW, vv0, vv1, inverted));
            }
```
And
```java
protected void drawStackHorizontal(List values, Comparable category,
            Graphics2D g2, CategoryItemRendererState state,
            Rectangle2D dataArea, CategoryPlot plot,
            CategoryAxis domainAxis, ValueAxis rangeAxis,
            CategoryDataset dataset) {

            drawStack(category, dataset, state, dataArea, plot, domainAxis, rangeAxis, values, g2, (double barX0, double barW, double vv0, double vv1, boolean inverted) -> createHorizontalBlock(barX0, barW, vv0, vv1, inverted));
            }
```

Case study 4:

There is a bug inside of a method which is duplicated with another method, they fixed one, but didn't fix another one.

![alt](https://i.imgur.com/ZrBFRIh.png)

And there is another inconsistences:

![alt](https://i.imgur.com/kvRd72k.png)

Obviously, the `break` should be inside of `{}`

The other differences are simple type II clones, we can just use parameters to replace the differences.

And, even though two classes have the same super class, but, their super class is not abstract, and superclass has many other subclasses. So it's not a good idea to put the extracted method to their super class.

Instead, we create an intermediate class for the two class, put the extracted method in it. At the same time, we should check other methods that are called inside the extracted method, check if we can pull them up as well, if we cannot, we should use template method design pattern.

The method being called inside of extracted method is:
`getClassLoaderFromJar()`, and it's identical in two methods, so we can easily pull it up to the super class we created.
