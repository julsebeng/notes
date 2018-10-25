# Qt Quick & QML For Beginners
## Table of Contents
- [Installing Qt on Linux and Mac](#installing-qt-on-linux-and-mac)
- [Running your QML Applications](#running-your-qml-applications)
- [Property Binding](#property-binding)
- [Reference](#reference)
- [Properties and Handlers](#properties-and-handlers)

---

## Property Binding
- Binding: having one property dependent on the value of another: `height: width * 1.5`
- **This binding can be broken**. Consider the example:
```
fooId.width: fooId.height * 1.5
...
fooId.width = 100
```
Since `fooId.width` is being set to a static value at some point after it's initially bound to `fooId.height`, the binding
between the two is broken and width will no longer scale with height.

Note that:
```
fooId.width = fooId.height * 1.5
```
will **not** fix the binding that has already been broken. Whenever this code is run it will take the current height, multiply
it by 1.5, and set the width to that value, but **the width will not implicitly change whenever the height changes.**
The binding can be restored with the following code:
```
fooId.width = Qt.binding( function() {
    return fooId.width * 1.5
} )

---

## Properties and Handlers
When a property is created, a signal for that property is also created:
```
property var foo: "foo"
onFooChanged: {
    console.log( foo )
}
```
This signal will be fired after the property value has changed.

---

## Reference

### Logging output to Qt Creator
`console.log( "log" + "message" )` to log a message to the Application Output pane when using Qt Quick.

### Ternary operations
`font.bold: isBold ? true : false`

### Data types

##### `var`
A `var` can refer to any data type.
```
property var foo: 10
property var bar: "bar"
property var baz: Qt.size( 10, 10 )
```

##### `rect`
Represents a rectangle. Essentially composes a point and a size.
```
property rect foo: Qt.rect( <X coord>, <Y coord>, <width>, <height> )
```

##### `point`
Represents a point in space.
```
property point foo: Qt.point( <X coord>, <Y coord> )
```

##### `size`
Represents a 2D size.
```
property size foo: Qt.size( <width>, <height> )
```

##### `font`
Contains font family, size, formatting, etc.
```
property font foo: Qt.font( { family: "consolas", pointSize: 30, bold: true } )
```

##### Arrays
Can hold data of differing types; this is a JavaScript array.
```
// New array:
property var foo: [ 1, 2, 3, "four", "five", ( function() { return "six"; } ) ]

// Length:
foo.length

// Range-based looping:
foo.forEach( function( value, index ) {
    print( value )
    // Calling the anonymous function stored in the array
    if( index === 5 ) {
        value()
    }
} )
```

### The Qt Global Object
Global object that provides useful functionality in Qt.
```
// Create data types
property var size: Qt.size( ... )
// Quit the application
Qt.quit()
// List available fonts
property var fonts: Qt.fontFamilies()
for( var i = 0; i < fonts.length ; i++ ) {
    console.log( fonts[ i ] )
}
// Generate MD5 hash of a string
property var foo: Qt.md5( someString )
// Open resource using an external program
Qt.openUrlExternally( "https://google.com" )
Qt.openUrlExternally( "file:///D:/picture/picture.png" )
// Capture platform info
Qt.platform.os
```

### Qt Quick basic elements