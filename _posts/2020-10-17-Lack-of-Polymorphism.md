Two Smells:
- Type checking smell
- State checking smell

### Case study 1: Type checking smell

> Anytime you find yourself writing code of the form "if the object is of type T1, then do something, but if it's of type T2, then do something else" slap yourself! From Effective C++, by Scott Meyers: 


Type checking smell:

```java
public ArrayList<Point2D> getPoints(){
    ArrayList<Point2D> a = new ArrayList<Point2D>();
    Node n = getEnd();
    Rectangle2D start = getStart().getBounds();
    Rectangle2D end = n.getBounds();

    if (n instanceof CallNode &&
            ((CallNode)n).getImplicitParameter() ==
            ((CallNode)getStart()).getImplicitParameter())
    {
        Point2D p = new Point2D.Double(start.getMaxX(), end.getY() - CallNode.CALL_YGAP / 2);
        Point2D q = new Point2D.Double(end.getMaxX(), end.getY());
        Point2D s = new Point2D.Double(q.getX() + end.getWidth(), q.getY());
        Point2D r = new Point2D.Double(s.getX(), p.getY());
        a.add(p);
        a.add(r);
        a.add(s);
        a.add(q);
    }
    else if (n instanceof PointNode) // show nicely in tool bar
    {
        a.add(new Point2D.Double(start.getMaxX(), start.getY()));
        a.add(new Point2D.Double(end.getX(), start.getY()));
    }
    else     
    {
        Direction d = new Direction(start.getX() - end.getX(), 0);
        Point2D endPoint = getEnd().getConnectionPoint(d);

        if (start.getCenterX() < endPoint.getX())
            a.add(new Point2D.Double(start.getMaxX(),
                    endPoint.getY()));
        else
            a.add(new Point2D.Double(start.getX(),
                    endPoint.getY()));
        a.add(endPoint);
    }
    return a;
}
```

For each block, they have some dependencies out of the block, for example, `a`. `start`, `end`,  the above case, we can move them to inside of the blocks.

Otherwise, we may have to pass parameters to the extracted interface.

```java
public ArrayList<Point2D> getPoints(){
    
    Node endNode = getEnd();
    Node startNode = getStart();
    

    if (endNode instanceof CallNode &&
            ((CallNode)endNode).getImplicitParameter() ==
            ((CallNode)startNode).getImplicitParameter())
    {
        ArrayList<Point2D> a = new ArrayList<Point2D>();
        Rectangle2D start = startNode.getBounds();
        Rectangle2D end = endNode.getBounds();
        Point2D p = new Point2D.Double(start.getMaxX(), end.getY() - CallNode.CALL_YGAP / 2);
        Point2D q = new Point2D.Double(end.getMaxX(), end.getY());
        Point2D s = new Point2D.Double(q.getX() + end.getWidth(), q.getY());
        Point2D r = new Point2D.Double(s.getX(), p.getY());
        a.add(p);
        a.add(r);
        a.add(s);
        a.add(q);
        return a;
    }
    else if (endNode instanceof PointNode) // show nicely in tool bar
    {
        ArrayList<Point2D> a = new ArrayList<Point2D>();
        Rectangle2D start = startNode.getBounds();
        Rectangle2D end = endNode.getBounds();
        a.add(new Point2D.Double(start.getMaxX(), start.getY()));
        a.add(new Point2D.Double(end.getX(), start.getY()));
        return a;
    }
    else     
    {
        ArrayList<Point2D> a = new ArrayList<Point2D>();
        Rectangle2D start = startNode.getBounds();
        Rectangle2D end = endNode.getBounds();
        Direction d = new Direction(start.getX() - end.getX(), 0);
        Point2D endPoint = endNode.getConnectionPoint(d);

        if (start.getCenterX() < endPoint.getX())
            a.add(new Point2D.Double(start.getMaxX(),
                    endPoint.getY()));
        else
            a.add(new Point2D.Double(start.getX(),
                    endPoint.getY()));
        a.add(endPoint);
        return a;
    }
    
}
```

And, CallNode, PointNode have a same super class, and it's abstract:

![alt](https://i.imgur.com/0dhW9YY.png)

for the `else`, we can put the implementation on their super class, and override the method inside of subclass.

```java
//AbstractNode.java
public ArrayList<Point2D> getPoints(Node startNode) {
    ArrayList<Point2D> a = new ArrayList<Point2D>();
    Rectangle2D start = startNode.getBounds();
    Rectangle2D end = this.getBounds();
    Direction d = new Direction(start.getX() - end.getX(), 0);
    Point2D endPoint = this.getConnectionPoint(d);
    if (start.getCenterX() < endPoint.getX())
        a.add(new Point2D.Double(start.getMaxX(), endPoint.getY()));
    else
        a.add(new Point2D.Double(start.getX(), endPoint.getY()));
    a.add(endPoint);
    return a;
}
```

```java
//CallNode.java
public ArrayList<Point2D> getPoints(Node startNode) {
    ArrayList<Point2D> a = new ArrayList<Point2D>();
    if (this.getImplicitParameter() == ((CallNode) startNode).getImplicitParameter()) {
        Rectangle2D start = startNode.getBounds();
        Rectangle2D end = this.getBounds();
        Point2D p = new Point2D.Double(start.getMaxX(), end.getY() - CallNode.CALL_YGAP / 2);
        Point2D q = new Point2D.Double(end.getMaxX(), end.getY());
        Point2D s = new Point2D.Double(q.getX() + end.getWidth(), q.getY());
        Point2D r = new Point2D.Double(s.getX(), p.getY());
        a.add(p);
        a.add(r);
        a.add(s);
        a.add(q);
    }
    return a;
}
```

```java
//PointNode.java
public ArrayList<Point2D> getPoints(Node startNode) {
    ArrayList<Point2D> a = new ArrayList<Point2D>();
    Rectangle2D start = startNode.getBounds();
    Rectangle2D end = this.getBounds();
    a.add(new Point2D.Double(start.getMaxX(), start.getY()));
    a.add(new Point2D.Double(end.getX(), start.getY()));
    return a;
}
```

We need to add the method to Node.interface
```java
///Node.java
ArrayList<Point2D> getPoints(Node startNode);
```

Now in original class:
```java
//CallEdge.java
public ArrayList<Point2D> getPoints(){
    
    Node endNode = getEnd();
    Node startNode = getStart();

    return endNode.getPoints(startNode);
}
```
### Case study 2: State checking smell
```java
public void mouseDragged(MouseEvent event){
    Point2D mousePoint = new Point2D.Double(event.getX() / zoom, 
            event.getY() / zoom);
    boolean isCtrl = (event.getModifiersEx() & InputEvent.CTRL_DOWN_MASK) != 0; 

    if (dragMode == DRAG_MOVE && lastSelected instanceof Node)
    {               
        Node lastNode = (Node) lastSelected;
        Rectangle2D bounds = lastNode.getBounds();
        double dx = mousePoint.getX() - lastMousePoint.getX();
        double dy = mousePoint.getY() - lastMousePoint.getY();

        // we don't want to drag nodes into negative coordinates
        // particularly with multiple selection, we might never be 
        // able to get them back.
        Iterator iter = selectedItems.iterator();
        while (iter.hasNext())
        {
            Object selected = iter.next();                 
            if (selected instanceof Node)
            {
                Node n = (Node) selected;
                bounds.add(n.getBounds());
            }
        }
        dx = Math.max(dx, -bounds.getX());
        dy = Math.max(dy, -bounds.getY());

        iter = selectedItems.iterator();
        while (iter.hasNext())
        {
            Object selected = iter.next();                 
            if (selected instanceof Node)
            {
                Node n = (Node) selected;
                // If the father is selected, don't move the children
                if (!selectedItems.contains(n.getParent()))
                {
                    n.translate(dx, dy);
                }
            }
        }
        // we don't want continuous layout any more because of multiple selection
        // graph.layout();
    }            
    else if (dragMode == DRAG_LASSO)
    {
        double x1 = mouseDownPoint.getX();
        double y1 = mouseDownPoint.getY();
        double x2 = mousePoint.getX();
        double y2 = mousePoint.getY();
        Rectangle2D.Double lasso = new Rectangle2D.Double(Math.min(x1, x2), 
                Math.min(y1, y2), Math.abs(x1 - x2) , Math.abs(y1 - y2));
        Iterator iter = graph.getNodes().iterator();
        while (iter.hasNext())
        {
            Node n = (Node) iter.next();
            Rectangle2D bounds = n.getBounds();
            if (!isCtrl && !lasso.contains(bounds)) 
            {
                removeSelectedItem(n);
            }
            else if (lasso.contains(bounds)) 
            {
                addSelectedItem(n);
            }
        }
    }

    lastMousePoint = mousePoint;
    repaint();
}
```

First conditional we may change it to:
```java
if (dragMode == DRAG_MOVE){
    if(lastSelected instanceof Node)
        {               
            Node lastNode = (Node) lastSelected;
            Rectangle2D bounds = lastNode.getBounds();
            double dx = mousePoint.getX() - lastMousePoint.getX();
            double dy = mousePoint.getY() - lastMousePoint.getY();
        }
}
```

We are checking the state of `dragMode`, drag mode has few cases, for each case, we may create a class.

But first, we create a common super class for all Mode.
```java
//DragMode.java
public abstract class DragMode {
	public abstract int getDragMode();

	public abstract void mouseDragged(Point2D mousePoint, boolean isCtrl, GraphPanel graphPanel);
} 
```
Note that in order to be compatible with previous code, we add `getDragMode()` method, return an int.

```java
//DragLasso.java
public class DragLasso extends DragMode {
	public int getDragMode() {
		return GraphPanel.DRAG_LASSO;
	}

	public void mouseDragged(Point2D mousePoint, boolean isCtrl, GraphPanel graphPanel) {
		double x1 = graphPanel.getMouseDownPoint().getX();
		double y1 = graphPanel.getMouseDownPoint().getY();
		double x2 = mousePoint.getX();
		double y2 = mousePoint.getY();
		Rectangle2D.Double lasso = new Rectangle2D.Double(Math.min(x1, x2), Math.min(y1, y2), Math.abs(x1 - x2),
				Math.abs(y1 - y2));
		Iterator iter = graphPanel.getGraph().getNodes().iterator();
		while (iter.hasNext()) {
			Node n = (Node) iter.next();
			Rectangle2D bounds = n.getBounds();
			if (!isCtrl && !lasso.contains(bounds)) {
				graphPanel.removeSelectedItem(n);
			} else if (lasso.contains(bounds)) {
				graphPanel.addSelectedItem(n);
			}
		}
	}
} 
```
```java
public class DragMove extends DragMode {
	public int getDragMode() {
		return GraphPanel.DRAG_MOVE;
	}

	public void mouseDragged(Point2D mousePoint, boolean isCtrl, GraphPanel graphPanel) {
		if (graphPanel.getLastSelected() instanceof Node) {
			Node lastNode = (Node) graphPanel.getLastSelected();
			Rectangle2D bounds = lastNode.getBounds();
			double dx = mousePoint.getX() - graphPanel.getLastMousePoint().getX();
			double dy = mousePoint.getY() - graphPanel.getLastMousePoint().getY();
			Iterator iter = graphPanel.getSelectedItems().iterator();
			while (iter.hasNext()) {
				Object selected = iter.next();
				if (selected instanceof Node) {
					Node n = (Node) selected;
					bounds.add(n.getBounds());
				}
			}
			dx = Math.max(dx, -bounds.getX());
			dy = Math.max(dy, -bounds.getY());
			iter = graphPanel.getSelectedItems().iterator();
			while (iter.hasNext()) {
				Object selected = iter.next();
				if (selected instanceof Node) {
					Node n = (Node) selected;
					if (!graphPanel.getSelectedItems().contains(n.getParent())) {
						n.translate(dx, dy);
					}
				}
			}
		}
	}
} 
```

```java
public class DragNone extends DragMode {
	public int getDragMode() {
		return GraphPanel.DRAG_NONE;
	}

	public void mouseDragged(Point2D mousePoint, boolean isCtrl, GraphPanel graphPanel) {
	}
} 
```

```java
public class DragRubberband extends DragMode {
	public int getDragMode() {
		return GraphPanel.DRAG_RUBBERBAND;
	}

	public void mouseDragged(Point2D mousePoint, boolean isCtrl, GraphPanel graphPanel) {
	}
} 
```

In original class, we change dragMode from int to `DragMode` type.
```java
private int dragMode;
private DragMode dragMode = new DragNone();
```

Apply polymorphism in original method:
```java
public void mouseDragged(MouseEvent event)
{
    Point2D mousePoint = new Point2D.Double(event.getX() / zoom, 
            event.getY() / zoom);
    boolean isCtrl = (event.getModifiersEx() & InputEvent.CTRL_DOWN_MASK) != 0; 
    dragMode.mouseDragged(mousePoint, isCtrl, this);
```

In order to be compatible with old code, we must change all the places that is using `dragMode` with `dragMode.getDragMode()`, and refactor `setDragMode(int i)` to:
```java
public void setDragMode(int dragMode) {
    switch (dragMode) {
    case DRAG_LASSO:
        this.dragMode = new DragLasso();
        break;
    case DRAG_MOVE:
        this.dragMode = new DragMove();
        break;
    case DRAG_RUBBERBAND:
        this.dragMode = new DragRubberband();
        break;
    case DRAG_NONE:
        this.dragMode = new DragNone();
        break;
    default:
        this.dragMode = null;
        break;
    }
}
```

We can improve the method call:

```java
dragMode.mouseDragged(mousePoint, isCtrl, this);
```

Now it has three parameters, but we can remove `this` by pass it to the constructor of `DragMode`:

```java
//DragMode.java
public abstract class DragMode {
    private GraphPanel graphPanel;

    public DragMode(GraphPanel graphPanel) {
		this.graphPanel = graphPanel;
	}

	public abstract int getDragMode();

	public abstract void mouseDragged(Point2D mousePoint, boolean isCtrl, GraphPanel graphPanel);
} 
```

So in subclasses:

```java
public class DragRubberband extends DragMode {


    public DragRubberband(GraphPanel graphPanel) {
		super(graphPanel);
	}

	public int getDragMode() {
		return GraphPanel.DRAG_RUBBERBAND;
	}

	public void mouseDragged(Point2D mousePoint, boolean isCtrl) {
	}
} 
```

Now the method call become:
```java
dragMode.mouseDragged(mousePoint, isCtrl);
```

And the `setDragMode()` become:

```java
public void setDragMode(int dragMode) {
    switch (dragMode) {
    case DRAG_LASSO:
        this.dragMode = new DragLasso(this);
        break;
    case DRAG_MOVE:
        this.dragMode = new DragMove(this);
        break;
    case DRAG_RUBBERBAND:
        this.dragMode = new DragRubberband(this);
        break;
    case DRAG_NONE:
        this.dragMode = new DragNone(this);
        break;
    default:
        this.dragMode = null;
        break;
    }
}
```