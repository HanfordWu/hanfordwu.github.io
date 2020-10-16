#### Duplicated methods reside in one class
Case Study 1:
There are two methods in same class:
Method 1:
```java
 protected List refreshTicksHorizontal(Graphics2D g2,
                Rectangle2D dataArea, RectangleEdge edge) {

        List result = new java.util.ArrayList();

        Font tickLabelFont = getTickLabelFont();
        g2.setFont(tickLabelFont);

        if (isAutoTickUnitSelection()) {
            selectAutoTickUnit(g2, dataArea, edge);
        }

        DateTickUnit unit = getTickUnit();
        Date tickDate = calculateLowestVisibleTickValue(unit);
        Date upperDate = getMaximumDate();

        while (tickDate.before(upperDate)) {
            // could add a flag to make the following correction optional...
            tickDate = correctTickDateForPosition(tickDate, unit,
                    this.tickMarkPosition);

            long lowestTickTime = tickDate.getTime();
            long distance = unit.addToDate(tickDate, this.timeZone).getTime()
                    - lowestTickTime;
            int minorTickSpaces = getMinorTickCount();
            if (minorTickSpaces <= 0) {
                minorTickSpaces = unit.getMinorTickCount();
            }
            for (int minorTick = 1; minorTick < minorTickSpaces; minorTick++) {
                long minorTickTime = lowestTickTime - distance
                        * minorTick / minorTickSpaces;
                if (minorTickTime > 0 && getRange().contains(minorTickTime)
                        && (!isHiddenValue(minorTickTime))) {
                    result.add(new DateTick(TickType.MINOR,
                            new Date(minorTickTime), "", TextAnchor.TOP_CENTER,
                            TextAnchor.CENTER, 0.0));
                }
            }

            if (!isHiddenValue(tickDate.getTime())) {
                // work out the value, label and position
                String tickLabel;
                DateFormat formatter = getDateFormatOverride();
                if (formatter != null) {
                    tickLabel = formatter.format(tickDate);
                }
                else {
                    tickLabel = this.tickUnit.dateToString(tickDate);
                }
                TextAnchor anchor = null;
                TextAnchor rotationAnchor = null;
                double angle = 0.0;
                if (isVerticalTickLabels()) {
                    anchor = TextAnchor.CENTER_RIGHT;
                    rotationAnchor = TextAnchor.CENTER_RIGHT;
                    if (edge == RectangleEdge.TOP) {
                        angle = Math.PI / 2.0;
                    }
                    else {
                        angle = -Math.PI / 2.0;
                    }
                }
                else {
                    if (edge == RectangleEdge.TOP) {
                        anchor = TextAnchor.BOTTOM_CENTER;
                        rotationAnchor = TextAnchor.BOTTOM_CENTER;
                    }
                    else {
                        anchor = TextAnchor.TOP_CENTER;
                        rotationAnchor = TextAnchor.TOP_CENTER;
                    }
                }

                Tick tick = new DateTick(tickDate, tickLabel, anchor,
                        rotationAnchor, angle);
                result.add(tick);

                long currentTickTime = tickDate.getTime();
                tickDate = unit.addToDate(tickDate, this.timeZone);
                long nextTickTime = tickDate.getTime();
                for (int minorTick = 1; minorTick < minorTickSpaces;
                        minorTick++) {
                    long minorTickTime = currentTickTime
                            + (nextTickTime - currentTickTime)
                            * minorTick / minorTickSpaces;
                    if (getRange().contains(minorTickTime)
                            && (!isHiddenValue(minorTickTime))) {
                        result.add(new DateTick(TickType.MINOR,
                                new Date(minorTickTime), "",
                                TextAnchor.TOP_CENTER, TextAnchor.CENTER,
                                0.0));
                    }
                }

            }
            else {
                tickDate = unit.rollDate(tickDate, this.timeZone);
            }

        }
        return result;

    }
```
Method 2:
```java
protected List refreshTicksVertical(Graphics2D g2,
            Rectangle2D dataArea, RectangleEdge edge) {

        List result = new java.util.ArrayList();

        Font tickLabelFont = getTickLabelFont();
        g2.setFont(tickLabelFont);

        if (isAutoTickUnitSelection()) {
            selectAutoTickUnit(g2, dataArea, edge);
        }
        DateTickUnit unit = getTickUnit();
        Date tickDate = calculateLowestVisibleTickValue(unit);
        Date upperDate = getMaximumDate();

        while (tickDate.before(upperDate)) {

            // could add a flag to make the following correction optional...
            tickDate = correctTickDateForPosition(tickDate, unit,
                    this.tickMarkPosition);

            long lowestTickTime = tickDate.getTime();
            long distance = unit.addToDate(tickDate, this.timeZone).getTime()
                    - lowestTickTime;
            int minorTickSpaces = getMinorTickCount();
            if (minorTickSpaces <= 0) {
                minorTickSpaces = unit.getMinorTickCount();
            }
            for (int minorTick = 1; minorTick < minorTickSpaces; minorTick++) {
                long minorTickTime = lowestTickTime - distance
                        * minorTick / minorTickSpaces;
                if (minorTickTime > 0 && getRange().contains(minorTickTime)
                        && (!isHiddenValue(minorTickTime))) {
                    result.add(new DateTick(TickType.MINOR,
                            new Date(minorTickTime), "", TextAnchor.TOP_CENTER,
                            TextAnchor.CENTER, 0.0));
                }
            }
            if (!isHiddenValue(tickDate.getTime())) {
                // work out the value, label and position
                String tickLabel;
                DateFormat formatter = getDateFormatOverride();
                if (formatter != null) {
                    tickLabel = formatter.format(tickDate);
                }
                else {
                    tickLabel = this.tickUnit.dateToString(tickDate);
                }
                TextAnchor anchor = null;
                TextAnchor rotationAnchor = null;
                double angle = 0.0;
                if (isVerticalTickLabels()) {
                    anchor = TextAnchor.BOTTOM_CENTER;
                    rotationAnchor = TextAnchor.BOTTOM_CENTER;
                    if (edge == RectangleEdge.LEFT) {
                        angle = -Math.PI / 2.0;
                    }
                    else {
                        angle = Math.PI / 2.0;
                    }
                }
                else {
                    if (edge == RectangleEdge.LEFT) {
                        anchor = TextAnchor.CENTER_RIGHT;
                        rotationAnchor = TextAnchor.CENTER_RIGHT;
                    }
                    else {
                        anchor = TextAnchor.CENTER_LEFT;
                        rotationAnchor = TextAnchor.CENTER_LEFT;
                    }
                }

                Tick tick = new DateTick(tickDate, tickLabel, anchor,
                        rotationAnchor, angle);
                result.add(tick);
                long currentTickTime = tickDate.getTime();
                tickDate = unit.addToDate(tickDate, this.timeZone);
                long nextTickTime = tickDate.getTime();
                for (int minorTick = 1; minorTick < minorTickSpaces;
                        minorTick++) {
                    long minorTickTime = currentTickTime
                            + (nextTickTime - currentTickTime)
                            * minorTick / minorTickSpaces;
                    if (getRange().contains(minorTickTime)
                            && (!isHiddenValue(minorTickTime))) {
                        result.add(new DateTick(TickType.MINOR,
                                new Date(minorTickTime), "",
                                TextAnchor.TOP_CENTER, TextAnchor.CENTER,
                                0.0));
                    }
                }
            }
            else {
                tickDate = unit.rollDate(tickDate, this.timeZone);
            }
        }
        return result;
    }
```

Obviously, most code of two methods are duplicated, so we can extract a common method for them. First, the differences are below:
![alt](https://i.imgur.com/ZYdSdo1.png)

According to the differences, we may have 6 parameters for the extracted method:
`private List refresh(Graphics2D g2, Rectangle2D dataArea, RectangleEdge edge, TextAnchor  arg0,
			RectangleEdge arg1, TextAnchor arg2, TextAnchor arg3, double arg4)`


The first method will call: `refresh(g2, dataArea, edge, TextAnchor.CENTER_RIGHT, RectangleEdge.TOP, Math.PI, -Math.PI, TextAnchor.BOTTOM_CENTER, TextAnchor.TOP_CENTER);`

The second method will call: `refresh(g2, dataArea, edge, TextAnchor.BOTTOM_CENTER, RectangleEdge.LEFT, -Math.PI, Math.PI, TextAnchor.CENTER_RIGHT, TextAnchor.CENTER_LEFT);`

There is another way to refactor:

The two duplicated methods has part of the code differences, from line 62 to line 78, we can extract them to two methods. Since we extract method can only return one result, so we need to figure out which part we should extract, so that part can return one result.

We find this part can be returning one result:
```java
TextAnchor anchor = null;
                TextAnchor rotationAnchor = null;
                double angle = 0.0;
                if (isVerticalTickLabels()) {
                    anchor = TextAnchor.CENTER_RIGHT;
                    rotationAnchor = TextAnchor.CENTER_RIGHT;
                    if (edge == RectangleEdge.TOP) {
                        angle = Math.PI / 2.0;
                    }
                    else {
                        angle = -Math.PI / 2.0;
                    }
                }
                else {
                    if (edge == RectangleEdge.TOP) {
                        anchor = TextAnchor.BOTTOM_CENTER;
                        rotationAnchor = TextAnchor.BOTTOM_CENTER;
                    }
                    else {
                        anchor = TextAnchor.TOP_CENTER;
                        rotationAnchor = TextAnchor.TOP_CENTER;
                    }
                }

                Tick tick = new DateTick(tickDate, tickLabel, anchor,
                        rotationAnchor, angle);
```
The returned result is tick. So we extract this part first.

Extracting for Method 1:
```java
private Tick createTickHorizontal(RectangleEdge edge, Date tickDate, String tickLabel) {
		TextAnchor anchor;
		TextAnchor rotationAnchor;
		double angle = 0.0;
		if (isVerticalTickLabels()) {
		    anchor = TextAnchor.CENTER_RIGHT;
		    rotationAnchor = TextAnchor.CENTER_RIGHT;
		    if (edge == RectangleEdge.TOP) {
		        angle = Math.PI / 2.0;
		    }
		    else {
		        angle = -Math.PI / 2.0;
		    }
		}
		else {
		    if (edge == RectangleEdge.TOP) {
		        anchor = TextAnchor.BOTTOM_CENTER;
		        rotationAnchor = TextAnchor.BOTTOM_CENTER;
		    }
		    else {
		        anchor = TextAnchor.TOP_CENTER;
		        rotationAnchor = TextAnchor.TOP_CENTER;
		    }
		}

		Tick tick = new DateTick(tickDate, tickLabel, anchor,
		        rotationAnchor, angle);
		return tick;
	}

```

Extracting for method 2:
```java
private Tick createTickVertical(RectangleEdge edge, Date tickDate, String tickLabel) {
		TextAnchor anchor;
		TextAnchor rotationAnchor;
		double angle = 0.0;
		if (isVerticalTickLabels()) {
		    anchor = TextAnchor.BOTTOM_CENTER;
		    rotationAnchor = TextAnchor.BOTTOM_CENTER;
		    if (edge == RectangleEdge.LEFT) {
		        angle = -Math.PI / 2.0;
		    }
		    else {
		        angle = Math.PI / 2.0;
		    }
		}
		else {
		    if (edge == RectangleEdge.LEFT) {
		        anchor = TextAnchor.CENTER_RIGHT;
		        rotationAnchor = TextAnchor.CENTER_RIGHT;
		    }
		    else {
		        anchor = TextAnchor.CENTER_LEFT;
		        rotationAnchor = TextAnchor.CENTER_LEFT;
		    }
		}

		Tick tick = new DateTick(tickDate, tickLabel, anchor,
		        rotationAnchor, angle);
		return tick;
	}
```
After this, the remaining of method 1 and 2 only have one line different, and this different line are calling different methods.

Now we extract duplicated code for remaining method 1 and 2. Because they have only one different one method call, so we can use lambda expression, let the functional interface call method in side extracted method.

1. create a functional interface:
```java
private interface Interface0 {
		Tick apply(Date tickDate, String tickLabel);
	}
```
2. The extracted method:
```java
private java.util.List refresh(Graphics2D g2, Rectangle2D dataArea, RectangleEdge edge, Interface0 arg0){
    List result = new java.util.ArrayList();

        Font tickLabelFont = getTickLabelFont();
        g2.setFont(tickLabelFont);

        if (isAutoTickUnitSelection()) {
            selectAutoTickUnit(g2, dataArea, edge);
        }

        DateTickUnit unit = getTickUnit();
        Date tickDate = calculateLowestVisibleTickValue(unit);
        Date upperDate = getMaximumDate();

        while (tickDate.before(upperDate)) {
            // could add a flag to make the following correction optional...
            tickDate = correctTickDateForPosition(tickDate, unit,
                    this.tickMarkPosition);

            long lowestTickTime = tickDate.getTime();
            long distance = unit.addToDate(tickDate, this.timeZone).getTime()
                    - lowestTickTime;
            int minorTickSpaces = getMinorTickCount();
            if (minorTickSpaces <= 0) {
                minorTickSpaces = unit.getMinorTickCount();
            }
            for (int minorTick = 1; minorTick < minorTickSpaces; minorTick++) {
                long minorTickTime = lowestTickTime - distance
                        * minorTick / minorTickSpaces;
                if (minorTickTime > 0 && getRange().contains(minorTickTime)
                        && (!isHiddenValue(minorTickTime))) {
                    result.add(new DateTick(TickType.MINOR,
                            new Date(minorTickTime), "", TextAnchor.TOP_CENTER,
                            TextAnchor.CENTER, 0.0));
                }
            }

            if (!isHiddenValue(tickDate.getTime())) {
                // work out the value, label and position
                String tickLabel;
                DateFormat formatter = getDateFormatOverride();
                if (formatter != null) {
                    tickLabel = formatter.format(tickDate);
                }
                else {
                    tickLabel = this.tickUnit.dateToString(tickDate);
                }
                Tick  tick = arg0.apply(); // call functional interface
                result.add(tick);

                long currentTickTime = tickDate.getTime();
                tickDate = unit.addToDate(tickDate, this.timeZone);
                long nextTickTime = tickDate.getTime();
                for (int minorTick = 1; minorTick < minorTickSpaces;
                        minorTick++) {
                    long minorTickTime = currentTickTime
                            + (nextTickTime - currentTickTime)
                            * minorTick / minorTickSpaces;
                    if (getRange().contains(minorTickTime)
                            && (!isHiddenValue(minorTickTime))) {
                        result.add(new DateTick(TickType.MINOR,
                                new Date(minorTickTime), "",
                                TextAnchor.TOP_CENTER, TextAnchor.CENTER,
                                0.0));
                    }
                }

            }
            else {
                tickDate = unit.rollDate(tickDate, this.timeZone);
            }

        }
        return result;
}
```

Refactored duplicated methods:
Method 1:
```java
protected List refreshTicksHorizontal(Graphics2D g2,Rectangle2D dataArea, RectangleEdge edge) {
return refresh(g2, dataArea, edge,(Date tickDate, String tickLabel) -> createTickHorizontal(edge, tickDate, tickLabel));
}
```
Method 2:
```java
protected List refreshTicksHorizontal(Graphics2D g2,Rectangle2D dataArea, RectangleEdge edge) {
return refresh(g2, dataArea, edge,(Date tickDate, String tickLabel) -> createTickVertical(edge, tickDate, tickLabel));
}
```

-------

#### Duplicated methods reside in two different classes

Two methods resides in two classes are duplicated:

Method 1:
```java
public void testGetFirstMillisecond() {
        Locale saved = Locale.getDefault();
        Locale.setDefault(Locale.UK);
        TimeZone savedZone = TimeZone.getDefault();
        TimeZone.setDefault(TimeZone.getTimeZone("Europe/London"));
        Day d = new Day(1, 3, 1970);
        assertEquals(5094000000L, d.getFirstMillisecond());
        Locale.setDefault(saved);
        TimeZone.setDefault(savedZone);
    }
```
Method 2:
```java
public void testGetFirstMillisecond() {
        Locale saved = Locale.getDefault();
        Locale.setDefault(Locale.UK);
        TimeZone savedZone = TimeZone.getDefault();
        TimeZone.setDefault(TimeZone.getTimeZone("Europe/London"));
        Hour h = new Hour(15, 1, 4, 2006);
        assertEquals(1143900000000L, h.getFirstMillisecond());
        Locale.setDefault(saved);
        TimeZone.setDefault(savedZone);
    }
```
The differences are just:
![alt](https://i.imgur.com/ms2BIgC.png)

But now the differences are type III, because we are having type differences. But note that, Day and Hour they have the same method: `getFirstMillisecond()`, and `Day.class`, `Hour.class` have the same super class: `RegularTimePeriod.class`. 
So, first we can replace Day and Hour with `RegularTimePeriod` type. 

Second, because duplicated methods resides two different class, in order to share some common code, we create an intermediate class for them:

```java
public abstract class IntermediateTestCase extends TestCase {
	public IntermediateTestCase(String name) {
		super(name);
	}

	protected void testGetFirstMillisecondExtracted(Supplier<RegularTimePeriod> arg0, long arg1) {
		Locale saved = Locale.getDefault();
		Locale.setDefault(Locale.UK);
		TimeZone savedZone = TimeZone.getDefault();
		TimeZone.setDefault(TimeZone.getTimeZone("Europe/London"));
		RegularTimePeriod d = arg0.get();
		assertEquals(arg1, d.getFirstMillisecond());
		Locale.setDefault(saved);
		TimeZone.setDefault(savedZone);
	}
} 
```

Third, note that the extracted method we are having a parameter `Supplier`, this supplier just for supplying the RegularTimePeriod that we need in two different method. Through arg0, we can get the thing we need.

```java
public class HourTests extends IntermediateTestCase {
public void testGetFirstMillisecond() {
    testGetFirstMillisecondExtracted(() -> new Hour(15, 1, 4, 2006), 1143900000000L);
    }
}
```
```java
public class DayTests extends IntermediateTestCase {
public void testGetFirstMillisecond() {
    testGetFirstMillisecondExtracted(() -> new Day(1, 3, 1970), 5094000000L);
    }
}
```

Or if we don't use lambda expression, we can have **Template method design pattern**.

Inside the intermediate class we created:
```java
public abstract class IntermediateTestCase extends TestCase {
	public IntermediateTestCase(String name) {
		super(name);
	}

	protected void testGetFirstMillisecondExtracted(long arg1) {
		Locale saved = Locale.getDefault();
		Locale.setDefault(Locale.UK);
		TimeZone savedZone = TimeZone.getDefault();
		TimeZone.setDefault(TimeZone.getTimeZone("Europe/London"));
		RegularTimePeriod d = createTimePeriod();
		assertEquals(arg1, d.getFirstMillisecond());
		Locale.setDefault(saved);
		TimeZone.setDefault(savedZone);
	}
    public abstract RegularTimePeriod createTimePeriod();
} 
```
we leave createTimePeriod to subclass to specify.

```java
public class HourTests extends IntermediateTestCase {
    testGetFirstMillisecondExtracted(1143900000000L);
}
public RegularTimePeriod createTimePeriod() {
    	return new Hour(15, 1, 4, 2006);
    }
}
```

```java
public class DayTests extends IntermediateTestCase {
    testGetFirstMillisecondExtracted(5094000000L);
}
public RegularTimePeriod createTimePeriod() {
    	return new Day(1, 3, 1970);
    }
}
```