# Flutter ConstraintLayout

Build flexible layouts with constraints, Similar to Android ConstraintLayout.

No matter how complex the layout is and how deep the constraints are, it has almost the same
performance as Flex and Stack. When facing complex layouts, it provides better performance,
flexibility, and a very flat code hierarchy than Flex and Stack. Say no to 'nesting hell'.

**It is recommended to use ConstraintLayout at the top level. For extremely complex layout(A
thousand child elements, a thousand constraints), layout and drawing total time within 5
milliseconds（debug mode on Windows 10，release mode take less time）, the frame rate can be easily
reached 200 fps.**

**If not necessary, try to be relative to the parent layout, so that you can define less id.**

**Warning**:
For layout performance considerations, constraints are always one-way, and there is no two child
elements that directly or indirectly restrain each other. Each constraint should describe exactly
where the child elements are located. Although constraints can only be one-way, you can still better
handle things that were previously (Android ConstraintLayout) two-way constraints, such as chains.

**Warning**:
When the layout is complex, if the child elements need to be repainted frequently, it is recommended
to use RepaintBoundary to improve performance.

Anyone who sends you a harassing message, you can send him Flutter code and use nested hell to rule
him.

# Feature

1. build flexible layouts with constraints
    1. leftToLeft
    2. leftToRight
    3. rightToLeft
    4. rightToRight
    5. topToTop
    6. topToBottom
    7. bottomToTop
    8. bottomToBottom
    9. baselineToTop
    10. baselineToBottom
    11. baselineToBaseline
2. margin and goneMargin
3. clickPadding (quickly expand the click area of child elements without changing their actual size.
   This means that the click area can be shared between child elements without increasing nesting.)
4. visibility control
5. constraint integrity hint
6. bias
7. z-index
8. translate
9. percentage layout
10. guideline
11. constraints and widgets separation
12. barrier

Coming soon:

1. constraints visualization
2. chain
3. dimension ratio
4. more...

Support platform:

1. Android
2. iOS
3. Mac
4. Windows
5. Linux
6. Web

# Import

Null-safety

```yaml
dependencies:
  flutter_constraintlayout:
    git:
      url: 'https://github.com/hackware1993/Flutter-ConstraintLayout.git'
      ref: 'v0.8.2-stable'
```

# Example

```dart
import 'package:flutter/material.dart';
import 'package:flutter_constraintlayout/src/constraint_layout/constraint_layout.dart';

class Example extends StatefulWidget {
  const Example({Key? key}) : super(key: key);

  @override
  State createState() => ExampleState();
}

class ExampleState extends State<Example> {
  double x = 0;
  double y = 0;

  ConstraintId box0 = ConstraintId();
  ConstraintId box1 = ConstraintId();
  ConstraintId box2 = ConstraintId();
  ConstraintId box3 = ConstraintId();
  ConstraintId box4 = ConstraintId();
  ConstraintId box5 = ConstraintId();
  ConstraintId box6 = ConstraintId();
  ConstraintId box7 = ConstraintId();
  ConstraintId box8 = ConstraintId();
  ConstraintId box9 = ConstraintId();
  ConstraintId box10 = ConstraintId();
  ConstraintId box11 = ConstraintId();

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        backgroundColor: Colors.black,
        body: ConstraintLayout(
          // Constraints can be separated from widgets
          childConstraints: [
            Constraint(
              id: box0,
              width: 200,
              height: 200,
              bottomLeftTo: parent,
              zIndex: 20,
            )
          ],
          children: [
            Container(
              color: Colors.redAccent,
              alignment: Alignment.center,
              child: const Text('box0'),
            ).applyConstraintId(
              id: box0, // Constraints can be separated from widgets
            ),
            Container(
              color: Colors.redAccent,
              alignment: Alignment.center,
              child: const Text('box1'),
            ).apply(
              constraint: Constraint(
                // Constraints set with widgets
                id: box1,
                width: 200,
                height: 100,
                topRightTo: parent,
              ),
            ),
            Container(
              color: Colors.blue,
              alignment: Alignment.center,
              child: const Text('box2'),
            ).applyConstraint(
              // Constraints set with widgets easy way
              id: box2,
              width: matchConstraint,
              height: matchConstraint,
              centerHorizontalTo: box3,
              top: box3.bottom,
              bottom: parent.bottom,
            ),
            Container(
              color: Colors.orange,
              width: 200,
              height: 150,
              alignment: Alignment.center,
              child: const Text('box3'),
            ).applyConstraint(
              id: box3,
              width: wrapContent,
              height: wrapContent,
              right: box1.left,
              top: box1.bottom,
            ),
            Container(
              color: Colors.redAccent,
              alignment: Alignment.center,
              child: const Text('box4'),
            ).applyConstraint(
              id: box4,
              width: 50,
              height: 50,
              bottomRightTo: parent,
            ),
            GestureDetector(
              child: Container(
                color: Colors.pink,
                alignment: Alignment.center,
                child: const Text('box5 draggable'),
              ),
              onPanUpdate: (details) {
                setState(() {
                  x += details.delta.dx;
                  y += details.delta.dy;
                });
              },
            ).applyConstraint(
              id: box5,
              width: 120,
              height: 100,
              centerTo: parent,
              zIndex: 100,
              translate: Offset(x, y),
              translateConstraint: true,
            ),
            Container(
              color: Colors.lightGreen,
              alignment: Alignment.center,
              child: const Text('box6'),
            ).applyConstraint(
              id: box6,
              width: 120,
              height: 120,
              centerVerticalTo: box2,
              verticalBias: 0.8,
              left: box3.right,
              right: parent.right,
            ),
            Container(
              color: Colors.lightGreen,
              alignment: Alignment.center,
              child: const Text('box7'),
            ).applyConstraint(
              id: box7,
              width: matchConstraint,
              height: matchConstraint,
              left: parent.left,
              right: box3.left,
              centerVerticalTo: parent,
              margin: const EdgeInsets.all(50),
            ),
            Container(
              color: Colors.cyan,
              alignment: Alignment.center,
              child: const Text('child[7] pinned to the top right'),
            ).applyConstraint(
              width: 200,
              height: 100,
              left: box5.right,
              bottom: box5.top,
            ),
            const Text(
              'box9 baseline to box7',
              style: TextStyle(
                color: Colors.white,
              ),
            ).applyConstraint(
              id: box9,
              width: wrapContent,
              height: wrapContent,
              baseline: box7.baseline,
              left: box7.left,
            ),
            ...horizontalChain(
              centerHorizontalTo: parent,
              hChainList: [
                Container(
                  color: Colors.redAccent,
                  alignment: Alignment.center,
                  child: const Text('chain item 1'),
                ).applyConstraint(
                  id: box10,
                  width: matchConstraint,
                  height: 200,
                  top: parent.top,
                ),
                Container(
                  color: Colors.redAccent,
                  alignment: Alignment.center,
                  child: const Text('chain item 2'),
                ).applyConstraint(
                  id: box11,
                  width: matchConstraint,
                  height: 200,
                  top: parent.top,
                ),
              ],
            ),
            Container(
              color: Colors.yellow,
              alignment: Alignment.bottomCenter,
              child: const Text(
                  'percentage layout\nwidth: 50% of parent\nheight: 30% of parent'),
            ).applyConstraint(
              width: matchConstraint,
              height: matchConstraint,
              widthPercent: 0.5,
              heightPercent: 0.3,
              horizontalBias: 0,
              verticalBias: 0,
              centerTo: parent,
            ),
          ],
        ),
      ),
    );
  }
}
```

![example.webp](https://github.com/hackware1993/flutter-constraintlayout/blob/master/effect.gif?raw=true)

# Advanced usage

1. guideline

```dart
import 'package:flutter/material.dart';
import 'package:flutter_constraintlayout/src/constraint_layout/constraint_layout.dart';

class GuidelineExample extends StatelessWidget {
  const GuidelineExample({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    ConstraintId guideline = ConstraintId();
    return MaterialApp(
      home: Scaffold(
        body: ConstraintLayout(
          children: [
            Container(
              color: const Color(0xFF005BBB),
            ).applyConstraint(
              width: matchParent,
              height: matchConstraint,
              top: parent.top,
              bottom: guideline.top,
            ),
            Guideline(
              id: guideline,
              horizontal: true,
              guidelinePercent: 0.5,
            ),
            Container(
              color: const Color(0xFFFFD500),
            ).applyConstraint(
              width: matchParent,
              height: matchConstraint,
              top: guideline.bottom,
              bottom: parent.bottom,
            ),
            const Text(
              'Stand with the people of Ukraine',
              style: TextStyle(
                fontSize: 40,
                color: Colors.white,
              ),
            ).applyConstraint(
              width: wrapContent,
              height: wrapContent,
              centerHorizontalTo: parent,
              bottom: guideline.bottom,
            )
          ],
        ),
      ),
    );
  }
}
```

![guideline.webp](https://github.com/hackware1993/flutter-constraintlayout/blob/master/guideline.webp?raw=true)

2. barrier

```dart
import 'package:flutter/material.dart';
import 'package:flutter_constraintlayout/src/constraint_layout/constraint_layout.dart';

class BarrierExample extends StatelessWidget {
  const BarrierExample({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    ConstraintId leftChild = ConstraintId();
    ConstraintId rightChild = ConstraintId();
    ConstraintId barrier = ConstraintId();
    return MaterialApp(
      home: Scaffold(
        body: ConstraintLayout(
          debugShowGuideline: true,
          children: [
            Container(
              color: const Color(0xFF005BBB),
            ).applyConstraint(
              id: leftChild,
              width: 200,
              height: 200,
              top: parent.top,
              left: parent.left,
            ),
            Container(
              color: const Color(0xFFFFD500),
            ).applyConstraint(
              id: rightChild,
              width: 200,
              height: matchConstraint,
              right: parent.right,
              top: parent.top,
              bottom: parent.bottom,
              heightPercent: 0.5,
              verticalBias: 0,
            ),
            Barrier(
              id: barrier,
              direction: BarrierDirection.bottom,
              referencedIds: [leftChild, rightChild],
            ),
            const Text(
              'Align to barrier',
              style: TextStyle(
                fontSize: 40,
                color: Colors.blue,
              ),
            ).applyConstraint(
              width: wrapContent,
              height: wrapContent,
              centerHorizontalTo: parent,
              top: barrier.bottom,
              goneMargin: const EdgeInsets.only(top: 20),
            )
          ],
        ),
      ),
    );
  }
}
```

![barrier.gif](https://github.com/hackware1993/flutter-constraintlayout/blob/master/barrier.gif?raw=true)

3. badge

```dart
import 'package:flutter/material.dart';
import 'package:flutter_constraintlayout/src/constraint_layout/constraint_layout.dart';

class BadgeExample extends StatelessWidget {
  const BadgeExample({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    ConstraintId anchor = ConstraintId();
    return MaterialApp(
      home: Scaffold(
        body: ConstraintLayout(
          children: [
            Container(
              color: Colors.yellow,
            ).applyConstraint(
              width: 200,
              height: 200,
              centerTo: parent,
              id: anchor,
            ),
            Container(
              color: Colors.green,
              child: const Text(
                'Indeterminate badge size',
                style: TextStyle(
                  color: Colors.black,
                  fontSize: 20,
                ),
              ),
            ).applyConstraint(
              width: wrapContent,
              height: wrapContent,
              left: anchor.right,
              bottom: anchor.top,
              translate: const Offset(-0.5, 0.5),
              percentageTranslate: true,
            )
          ],
        ),
      ),
    );
  }
}
```

![badge.webp](https://github.com/hackware1993/flutter-constraintlayout/blob/master/badge.webp?raw=true)

# Support me

If it helps you a lot, consider sponsoring me a cup of milk tea.
<br/>
[Paypal](https://www.paypal.com/paypalme/hackware1993)
<br/>
<br/>
![support.webp](https://github.com/hackware1993/flutter-constraintlayout/blob/master/support.webp?raw=true)

# Contact

hackware1993@gmail.com
<br/>
<br/>
![communication.webp](https://github.com/hackware1993/flutter-constraintlayout/blob/master/communication.webp?raw=true)

# License

```
MIT License

Copyright (c) 2022 hackware1993

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
