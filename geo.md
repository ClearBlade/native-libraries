The geo library provides core functionality used to calculate geometric spacial relationships.

* geo()
* geo.Circle()
* geo.Polygon()
* geo.LineSegment()
* geo.Point()
* geo.WithinCircle()
* geo.Within()
* geo.Nearest()
* geo.Farthest()
* geo.Distance()
* geo.BearingTo()

## geo(options)		

Creates a geo object to perform necessary geometric calculations.

**Parameters**

 - options: 'polar','cartesian'
 
**Example:**
~~~javascript
	var geoObj = new geo('polar');
~~~

~~~javascript
	var geoObj = new geo('cartesian');
~~~


## geoObj.Point( Lat, Long )

Define a point on a coordinate system

**Parameters**

 - LAT = number representing a latitude
 - LONG = number representing a longitude

**Example**
~~~javascript
	var geoObj = new geo('polar');
	var myPoint = geoObj.Point( 30.26675, -97.742439 );
~~~

## geoObj.Point( x, y )

Define a point on a coordinate system

**Parameters**

 - x = number representing x position
 - x = number representing y position

**Example**
~~~javascript
	var myPoint = geoObj.Point( 44, 76.4 );
~~~

## geoObj.Circle(Lat, Long, Radius)

Defines a shape of type circle on a coordinate system

**Parameters**

 - LAT = number representing a latitude
 - LONG = number representing a longitude
 - RADIUS = number in meters

**Example**
~~~javascript
	var geoObj = new geo('polar');

	var myCircle = geoObj.Circle( 30.26675, -97.742439, 1000 );
~~~

## geoObj.Circle(x, y, Radius)

Defines a shape of type circle on a coordinate system

**Parameters**

 - x = number representing x position of circle center
 - y = number representing y position in circle center
 - Radius = number in meters

**Example**
~~~javascript
	var geoObj = new geo('cartesian');
	var myCircle = geoObj.Circle( 50, 41.4, 1000 );
~~~

## geoObj.WithinCircle(Circle, Point)

Test for whether a point is within the bounds of a circle

**Parameters** 

 - Circle = Circle Shape
 - Point = a point Object at a location

**Example**
~~~javascript
	var myPoint = geoObj.Point( 34.26675, -91.742439 );
	var myCircle = geoObj.Circle( 30.26675, -97.742439, 4000 );

	var isInside = geoObj.WithinCircle(myCircle, myPoint);
~~~

## geoObj.Polygon([Point1, Point2, Point3 ...])

Define a polygon object made up of at least three point objects.  All shapes will be closed.

**Parameters**

 - [Points] = array of point objects that when connected according their order forms a closed shape.

**Example**
~~~javascript
	var geoObj = new geo('polar');
	var pointAustin  = geoObj.Point( 34.26675,  -91.742439 );		// Austin, Texas, USA
	var pointLincoln = geoObj.Point( 40.825763, -96.685198 );  		// Lincoln, Nebraska, USA
	var pointVegas	 = geoObj.Point( 36.169941, -115.13983 );		// Las Vegas, Nevada, USA

	var southwest = geoObj.Polygon( [ pointAustin, pointLincoln, pointVegas ] );
~~~

## geoObj.Within(Polygon, Point)

Check if a point exists within the bounds of a polygon.

**Parameters**

 - Polygon = a defined polygon object
 - Point = a defined point object

**Example**
~~~javascript
	var geoObj = new geo('polar');
	var southwest = geoObj.Polygon( [ point1, point2, point3 ] );
	var tulsa = geoObj.Point ( 36.153982, -95.992775 );

	var isSouthwest = geoObj.Within(southwestwest, tulsa);
~~~

## geoObj.LineSegment(Point1, Point2, Width)

Creates a line segment of a certain width.  Returns a polygon shape.  

**Parameters**

 - Point1 = the start Point object of a line segment
 - Point2 = the end Point object of a line segment
 - Width = a number in meters for the width of the line

**Example**
~~~javascript
	var geoObj = new geo('polar');
	var pointAustin  = geoObj.Point( 34.26675,  -91.742439 );		// Austin, Texas, USA
	var pointVegas	 = geoObj.Point( 36.169941, -115.13983) ;		// Las Vegas, Nevada, USA

	geoObj.LineSegment(pointAustin, pointVegas, 1000);  
~~~


## geoObj.Nearest(Point, [Point1, Point2, Point3, ...], Size)

Returns the nearest points from a list in relation to a source point.  The size parameter will limit the number of responses returned.

**Parameters**

 - Point = A source point to compare distances from
 - [Points] = A set point to search for distance from the source
 - Size = A number representing the return size of nearest points

**Example**
~~~javascript
	var pointAustin  = geoObj.Point( 34.26675,  -91.742439 );		// Austin, Texas, USA
	var pointLincoln = geoObj.Point( 40.825763, -96.685198 );  		// Lincoln, Nebraska, USA
	var pointVegas	 = geoObj.Point( 36.169941, -115.13983 );		// Las Vegas, Nevada, USA

	retun closestToAustin = geoObj.Nearest(pointAustin, [pointVegas, pointLincoln], 1);  
~~~

## geoObj.Farthest(Point, [Point1, Point2, Point3, ...], Size)

Returns the farthest points from a list in relation to a source point.  The size parameter will limit the number of responses returned. 

**Parameters**

 - Point = A source point to compare distances from
 - [Points] = A set point to search for distance from the source
 - Size = A number representing the return size of farthest points

**Example**
~~~javascript

	var pointAustin  = geoObj.Point( 34.26675,  -91.742439 );		// Austin, Texas, USA
	var pointLincoln = geoObj.Point( 40.825763, -96.685198 );  		// Lincoln, Nebraska, USA
	var pointVegas	 = geoObj.Point( 36.169941, -115.13983 );		// Las Vegas, Nevada, USA

	retun farthestFromAustin = geoObj.Nearest(pointAustin, [pointVegas, pointLincoln], 1); 
~~~

## geoObj.Distance(Point1, Point2)

Calculates the distance in meters between two points.  

**Parameters** 

 - Point1 = Point object for distance start calculation
 - Point2 = Point object for distance end calculation

**Example**
~~~javascript
	var geoObj = new geo('polar');
	var pointAustin  = geoObj.Point( 34.26675,  -91.742439 );		// Austin, Texas, USA
	var pointLincoln = geoObj.Point( 40.825763, -96.685198 );  		// Lincoln, Nebraska, USA
	
	var distance = geoObj.Distance(pointAustin, pointLincoln);
~~~

**Example**
~~~javascript
	var geoObj = new geo('cartesian');				// You can use any unit in Cartesian
	
	var point1  = geoObj.Point( 44,  -19 );			// Point on 2D cartesian graph
	var point2 = geoObj.Point( 23, 7 );  			// Point on 2D cartesian graph
	
	var distance = geoObj.Distance(point1, point2); // Distance in designated units
~~~

## geoObj.BearingTo(Point1, Point2)

Calculates the forward azimuth between two points in degrees.
Note that this function is only allowed in polar mode.

**Parameters**

 - Point1 = Point object for bearing start calculation
 - Point2 = Point object for bearing end calculation

**Example**

```javascript
var geoObj = new geo('polar');
var pointAustin  = geoObj.Point( 34.26675,  -91.742439 );		// Austin, Texas, USA
var pointLincoln = geoObj.Point( 40.825763, -96.685198 );  		// Lincoln, Nebraska, USA

var bearing = geoObj.BearingTo(pointAustin, pointLincoln);
// bearing will tell you direction from Austin to Lincoln in degrees
```
