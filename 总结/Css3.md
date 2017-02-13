今天在国外的网站上看到了很多看似简单却又非常强大的纯CSS绘制的图形，里面有最简单的矩形、圆形和三角形，也有各种常见的多边形，甚至是阴阳太极和网站小图标，真的非常强大，分享给大家。

## Square(正方形)

![40个纯CSS绘制的图形0](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-square.png)

```
#square {
    width: 100px;
    height: 100px;
    background: red;
}
```

## Rectangle(矩形)

![40个纯CSS绘制的图形1](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-rect.png)

```
#rectangle {
    width: 200px;
    height: 100px;
    background: red;
}
```

## Circle(圆形)

![40个纯CSS绘制的图形2](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-circle.png)

```
#circle {
    width: 100px;
    height: 100px;
    background: red;
    -moz-border-radius: 50px;
    -webkit-border-radius: 50px;
    border-radius: 50px;
}

/* 可以使用百分比值(大于50%)，但是低版本的Android不支持 */
```

## Oval(椭圆形)

![40个纯CSS绘制的图形3](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-oval.png)

```
#oval {
    width: 200px;
    height: 100px;
    background: red;
    -moz-border-radius: 100px / 50px;
    -webkit-border-radius: 100px / 50px;
    border-radius: 100px / 50px;
}

/* 可以使用百分比值(大于50%)，但是低版本的Android不支持 */
```

## Triangle Up(向上的三角形)

![40个纯CSS绘制的图形4](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-up.png)

```
#triangle-up {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 100px solid red;
}
```

## Triangle Down(向下)

![40个纯CSS绘制的图形5](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-down.png)

```
#triangle-down {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-top: 100px solid red;
}
```

## Triangle Left(向左)

![40个纯CSS绘制的图形6](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-left.png)

```
#triangle-left {
    width: 0;
    height: 0;
    border-top: 50px solid transparent;
    border-right: 100px solid red;
    border-bottom: 50px solid transparent;
}
```

## Triangle Right(向右)

![40个纯CSS绘制的图形7](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-right.png)

```
#triangle-right {
    width: 0;
    height: 0;
    border-top: 50px solid transparent;
    border-left: 100px solid red;
    border-bottom: 50px solid transparent;
}
```

## Triangle Top Left(左上)

![40个纯CSS绘制的图形8](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-top-left.png)

```
#triangle-topleft {
    width: 0;
    height: 0;
    border-top: 100px solid red;
    border-right: 100px solid transparent;
}
```

## Triangle Top Right(右上)

![40个纯CSS绘制的图形9](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-top-right.png)

```
#triangle-topright {
    width: 0;
    height: 0;
    border-top: 100px solid red;
    border-left: 100px solid transparent;
}
```

## Triangle Bottom Left(左下)

![40个纯CSS绘制的图形10](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-bottom-left.png)

```
#triangle-bottomleft {
    width: 0;
    height: 0;
    border-bottom: 100px solid red;
    border-right: 100px solid transparent;
}
```

## Triangle Bottom Right(右下)

![40个纯CSS绘制的图形11](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-triangle-bottom-right.png)

```
#triangle-bottomright {
    width: 0;
    height: 0;
    border-bottom: 100px solid red;
    border-left: 100px solid transparent;
}
```

## Curved Tail Arrow(弯尾箭头)

![40个纯CSS绘制的图形12](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-arrow-1.png)

![40个纯CSS绘制的图形13](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-arrow-2.png)

```
#curvedarrow {
  position: relative;
  width: 0;
  height: 0;
  border-top: 9px solid transparent;
  border-right: 9px solid red;
  -webkit-transform: rotate(10deg);
  -moz-transform: rotate(10deg);
  -ms-transform: rotate(10deg);
  -o-transform: rotate(10deg);
}
#curvedarrow:after {
  content: "";
  position: absolute;
  border: 0 solid transparent;
  border-top: 3px solid red;
  border-radius: 20px 0 0 0;
  top: -12px;
  left: -9px;
  width: 12px;
  height: 12px;
  -webkit-transform: rotate(45deg);
  -moz-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  -o-transform: rotate(45deg);
}
```

## Trapezoid(梯形)

![40个纯CSS绘制的图形14](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-trapezoid.png)

```
#trapezoid {
    border-bottom: 100px solid red;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    height: 0;
    width: 100px;
}
```

## Parallelogram(平行四边形)

![40个纯CSS绘制的图形15](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-parallelogram.png)

```
#parallelogram {
    width: 150px;
    height: 100px;
    -webkit-transform: skew(20deg);
       -moz-transform: skew(20deg);
         -o-transform: skew(20deg);
    background: red;
}
```

## Star (6-points)(六角星)

![40个纯CSS绘制的图形16](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-star-6.png)

```
#star-six {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 100px solid red;
    position: relative;
}
#star-six:after {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-top: 100px solid red;
    position: absolute;
    content: "";
    top: 30px;
    left: -50px;
}
```

## Star (5-points)(五角星)

![40个纯CSS绘制的图形17](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-star-5.png)

```
#star-five {
   margin: 50px 0;
   position: relative;
   display: block;
   color: red;
   width: 0px;
   height: 0px;
   border-right:  100px solid transparent;
   border-bottom: 70px  solid red;
   border-left:   100px solid transparent;
   -moz-transform:    rotate(35deg);
   -webkit-transform: rotate(35deg);
   -ms-transform:     rotate(35deg);
   -o-transform:      rotate(35deg);
}
#star-five:before {
   border-bottom: 80px solid red;
   border-left: 30px solid transparent;
   border-right: 30px solid transparent;
   position: absolute;
   height: 0;
   width: 0;
   top: -45px;
   left: -65px;
   display: block;
   content: '';
   -webkit-transform: rotate(-35deg);
   -moz-transform:    rotate(-35deg);
   -ms-transform:     rotate(-35deg);
   -o-transform:      rotate(-35deg);

}
#star-five:after {
   position: absolute;
   display: block;
   color: red;
   top: 3px;
   left: -105px;
   width: 0px;
   height: 0px;
   border-right: 100px solid transparent;
   border-bottom: 70px solid red;
   border-left: 100px solid transparent;
   -webkit-transform: rotate(-70deg);
   -moz-transform:    rotate(-70deg);
   -ms-transform:     rotate(-70deg);
   -o-transform:      rotate(-70deg);
   content: '';
}
```

## Pentagon(五边形)

![40个纯CSS绘制的图形18](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-pentagon.png)

```
#pentagon {
    position: relative;
    width: 54px;
    border-width: 50px 18px 0;
    border-style: solid;
    border-color: red transparent;
}
#pentagon:before {
    content: "";
    position: absolute;
    height: 0;
    width: 0;
    top: -85px;
    left: -18px;
    border-width: 0 45px 35px;
    border-style: solid;
    border-color: transparent transparent red;
}
```

## Hexagon(六边形)

![40个纯CSS绘制的图形19](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-hexagon.png)

```
#hexagon {
    width: 100px;
    height: 55px;
    background: red;
    position: relative;
}
#hexagon:before {
    content: "";
    position: absolute;
    top: -25px;
    left: 0;
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 25px solid red;
}
#hexagon:after {
    content: "";
    position: absolute;
    bottom: -25px;
    left: 0;
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-top: 25px solid red;
}
```

## Octagon(八边形)

![40个纯CSS绘制的图形20](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-octagon.png)

```
#octagon {
    width: 100px;
    height: 100px;
    background: red;
    position: relative;
}

#octagon:before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    border-bottom: 29px solid red;
    border-left: 29px solid #fff;
    border-right: 29px solid #fff;
    width: 42px;
    height: 0;
}

#octagon:after {
    content: "";
    position: absolute;
    bottom: 0;
    left: 0;
    border-top: 29px solid red;
    border-left: 29px solid #fff;
    border-right: 29px solid #fff;
    width: 42px;
    height: 0;
}
```

## Heart(心形)

![40个纯CSS绘制的图形21](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-heart.png)

```
#heart {
    position: relative;
    width: 100px;
    height: 90px;
}
#heart:before,
#heart:after {
    position: absolute;
    content: "";
    left: 50px;
    top: 0;
    width: 50px;
    height: 80px;
    background: red;
    -moz-border-radius: 50px 50px 0 0;
    border-radius: 50px 50px 0 0;
    -webkit-transform: rotate(-45deg);
       -moz-transform: rotate(-45deg);
        -ms-transform: rotate(-45deg);
         -o-transform: rotate(-45deg);
            transform: rotate(-45deg);
    -webkit-transform-origin: 0 100%;
       -moz-transform-origin: 0 100%;
        -ms-transform-origin: 0 100%;
         -o-transform-origin: 0 100%;
            transform-origin: 0 100%;
}
#heart:after {
    left: 0;
    -webkit-transform: rotate(45deg);
       -moz-transform: rotate(45deg);
        -ms-transform: rotate(45deg);
         -o-transform: rotate(45deg);
            transform: rotate(45deg);
    -webkit-transform-origin: 100% 100%;
       -moz-transform-origin: 100% 100%;
        -ms-transform-origin: 100% 100%;
         -o-transform-origin: 100% 100%;
            transform-origin :100% 100%;
}
```

## Infinity(无限符图形)

![40个纯CSS绘制的图形22](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-infinity.png)

```
#infinity {
    position: relative;
    width: 212px;
    height: 100px;
}

#infinity:before,
#infinity:after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 60px;
    height: 60px;
    border: 20px solid red;
    -moz-border-radius: 50px 50px 0 50px;
         border-radius: 50px 50px 0 50px;
    -webkit-transform: rotate(-45deg);
       -moz-transform: rotate(-45deg);
        -ms-transform: rotate(-45deg);
         -o-transform: rotate(-45deg);
            transform: rotate(-45deg);
}

#infinity:after {
    left: auto;
    right: 0;
    -moz-border-radius: 50px 50px 50px 0;
         border-radius: 50px 50px 50px 0;
    -webkit-transform: rotate(45deg);
       -moz-transform: rotate(45deg);
        -ms-transform: rotate(45deg);
         -o-transform: rotate(45deg);
            transform: rotate(45deg);
}
```

## Diamond Square(菱形)

![40个纯CSS绘制的图形23](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-diamond.png)

```
#diamond {
    width: 0;
    height: 0;
    border: 50px solid transparent;
    border-bottom-color: red;
    position: relative;
    top: -50px;
}
#diamond:after {
    content: '';
    position: absolute;
    left: -50px;
    top: 50px;
    width: 0;
    height: 0;
    border: 50px solid transparent;
    border-top-color: red;
}
```

## Diamond Shield（钻石盾牌）

![40个纯CSS绘制的图形24](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-shield.png)

```
#diamond-shield {
    width: 0;
    height: 0;
    border: 50px solid transparent;
    border-bottom: 20px solid red;
    position: relative;
    top: -50px;
}
#diamond-shield:after {
    content: '';
    position: absolute;
    left: -50px; top: 20px;
    width: 0;
    height: 0;
    border: 50px solid transparent;
    border-top: 70px solid red;
}
```

## Diamond Narrow

![40个纯CSS绘制的图形25](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-narrow.png)

```
#diamond-narrow {
    width: 0;
    height: 0;
    border: 50px solid transparent;
    border-bottom: 70px solid red;
    position: relative;
    top: -50px;
}
#diamond-narrow:after {
    content: '';
    position: absolute;
    left: -50px; top: 70px;
    width: 0;
    height: 0;
    border: 50px solid transparent;
    border-top: 70px solid red;
}
```

## Cut Diamond(砖石形)

![40个纯CSS绘制的图形26](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-cut.png)

```
#cut-diamond {
    border-style: solid;
    border-color: transparent transparent red transparent;
    border-width: 0 25px 25px 25px;
    height: 0;
    width: 50px;
    position: relative;
    margin: 20px 0 50px 0;
}
#cut-diamond:after {
    content: "";
    position: absolute;
    top: 25px;
    left: -25px;
    width: 0;
    height: 0;
    border-style: solid;
    border-color: red transparent transparent transparent;
    border-width: 70px 50px 0 50px;
}
```

## Egg(鸡蛋)

![40个纯CSS绘制的图形27](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-egg.png)

```
#egg {
   display:block;
   width: 126px;
   height: 180px;
   background-color: red;
   -webkit-border-radius: 63px 63px 63px 63px / 108px 108px 72px 72px;
   border-radius:         50%  50%  50%  50%  / 60%   60%   40%  40%;
}
```

## Pac-Man(吃豆人)

![40个纯CSS绘制的图形28](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-pac-man.png)

```
#pacman {
  width: 0px;
  height: 0px;
  border-right: 60px solid transparent;
  border-top: 60px solid red;
  border-left: 60px solid red;
  border-bottom: 60px solid red;
  border-top-left-radius: 60px;
  border-top-right-radius: 60px;
  border-bottom-left-radius: 60px;
  border-bottom-right-radius: 60px;
}
```

## Talk Bubble（聊天框）

![40个纯CSS绘制的图形29](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-talk-bubble.png)

```
#talkbubble {
   width: 120px;
   height: 80px;
   background: red;
   position: relative;
   -moz-border-radius:    10px;
   -webkit-border-radius: 10px;
   border-radius:         10px;
}
#talkbubble:before {
   content:"";
   position: absolute;
   right: 100%;
   top: 26px;
   width: 0;
   height: 0;
   border-top: 13px solid transparent;
   border-right: 26px solid red;
   border-bottom: 13px solid transparent;
}
```

## 12 Point Burst（爆炸形状）

![40个纯CSS绘制的图形30](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-12-point-burst.png)

```
#burst-12 {
    background: red;
    width: 80px;
    height: 80px;
    position: relative;
    text-align: center;
}
#burst-12:before, #burst-12:after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    height: 80px;
    width: 80px;
    background: red;
}
#burst-12:before {
    -webkit-transform: rotate(30deg);
       -moz-transform: rotate(30deg);
        -ms-transform: rotate(30deg);
         -o-transform: rotate(30deg);
}
#burst-12:after {
    -webkit-transform: rotate(60deg);
       -moz-transform: rotate(60deg);
        -ms-transform: rotate(60deg);
         -o-transform: rotate(60deg);
}
```

## Yin Yang(阴阳八卦)

![40个纯CSS绘制的图形31](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-yin-yang.png)

```
#yin-yang {
    width: 96px;
    height: 48px;
    background: #eee;
    border-color: red;
    border-style: solid;
    border-width: 2px 2px 50px 2px;
    border-radius: 100%;
    position: relative;
}

#yin-yang:before {
    content: "";
    position: absolute;
    top: 50%;
    left: 0;
    background: #eee;
    border: 18px solid red;
    border-radius: 100%;
    width: 12px;
    height: 12px;
}

#yin-yang:after {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    background: red;
    border: 18px solid #eee;
    border-radius:100%;
    width: 12px;
    height: 12px;
}
```

## Badge Ribbon(徽章丝带)

![40个纯CSS绘制的图形32](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-ribbon.png)

```
#badge-ribbon {
 position: relative;
 background: red;
 height: 100px;
 width: 100px;
 -moz-border-radius:    50px;
 -webkit-border-radius: 50px;
 border-radius:         50px;
}

#badge-ribbon:before,
#badge-ribbon:after {
  content: '';
  position: absolute;
  border-bottom: 70px solid red;
  border-left: 40px solid transparent;
  border-right: 40px solid transparent;
  top: 70px;
  left: -10px;
  -webkit-transform: rotate(-140deg);
  -moz-transform:    rotate(-140deg);
  -ms-transform:     rotate(-140deg);
  -o-transform:      rotate(-140deg);
}

#badge-ribbon:after {
  left: auto;
  right: -10px;
  -webkit-transform: rotate(140deg);
  -moz-transform:    rotate(140deg);
  -ms-transform:     rotate(140deg);
  -o-transform:      rotate(140deg);
}
```

## Space Invader（太空入侵者）

![40个纯CSS绘制的图形33](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-space-invader.png)

```
#space-invader{
  box-shadow:
    0 0 0 1em red,
    0 1em 0 1em red,
    -2.5em 1.5em 0 .5em red,
    2.5em 1.5em 0 .5em red,
    -3em -3em 0 0 red,
    3em -3em 0 0 red,
    -2em -2em 0 0 red,
    2em -2em 0 0 red,
    -3em -1em 0 0 red,
    -2em -1em 0 0 red,
    2em -1em 0 0 red,
    3em -1em 0 0 red,
    -4em 0 0 0 red,
    -3em 0 0 0 red,
    3em 0 0 0 red,
    4em 0 0 0 red,
    -5em 1em 0 0 red,
    -4em 1em 0 0 red,
    4em 1em 0 0 red,
    5em 1em 0 0 red,
    -5em 2em 0 0 red,
    5em 2em 0 0 red,
    -5em 3em 0 0 red,
    -3em 3em 0 0 red,
    3em 3em 0 0 red,
    5em 3em 0 0 red,
    -2em 4em 0 0 red,
    -1em 4em 0 0 red,
    1em 4em 0 0 red,
    2em 4em 0 0 red;

    background: red;
    width: 1em;
    height: 1em;
    overflow: hidden;

    margin: 50px 0 70px 65px;
  }
```

## TV Screen（电视屏幕）

![40个纯CSS绘制的图形34](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-tv-screen.png)

```
#tv {
  position: relative;
  width: 200px;
  height: 150px;
  margin: 20px 0;
  background: red;
  border-radius: 50% / 10%;
  color: white;
  text-align: center;
  text-indent: .1em;
}
#tv:before {
  content: '';
  position: absolute;
  top: 10%;
  bottom: 10%;
  right: -5%;
  left: -5%;
  background: inherit;
  border-radius: 5% / 50%;
}
```

## Chevron（雪佛龙）

![40个纯CSS绘制的图形35](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-chevron.png)

```
#chevron {
  position: relative;
  text-align: center;
  padding: 12px;
  margin-bottom: 6px;
  height: 60px;
  width: 200px;
}

#chevron:before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 51%;
  background: red;
  -webkit-transform: skew(0deg, 6deg);
  -moz-transform: skew(0deg, 6deg);
  -ms-transform: skew(0deg, 6deg);
  -o-transform: skew(0deg, 6deg);
  transform: skew(0deg, 6deg);
}
#chevron:after {
  content: '';
  position: absolute;
  top: 0;
  right: 0;
  height: 100%;
  width: 50%;
  background: red;
  -webkit-transform: skew(0deg, -6deg);
  -moz-transform: skew(0deg, -6deg);
  -ms-transform: skew(0deg, -6deg);
  -o-transform: skew(0deg, -6deg);
  transform: skew(0deg, -6deg);
}
```

## Magnifying Glass（放大镜）

![40个纯CSS绘制的图形36](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-glass.png)

```
#magnifying-glass{
 font-size: 10em; /* This controls the size. */
 display: inline-block;
 width: 0.4em;
 height: 0.4em;
 border: 0.1em solid red;
 position: relative;
 border-radius: 0.35em;
}
#magnifying-glass::before{
 content: "";
 display: inline-block;
 position: absolute;
 right: -0.25em;
 bottom: -0.1em;
 border-width: 0;
 background: red;
 width: 0.35em;
 height: 0.08em;
 -webkit-transform: rotate(45deg);
    -moz-transform: rotate(45deg);
     -ms-transform: rotate(45deg);
      -o-transform: rotate(45deg);
}
```

## Facebook Icon（Facebook图标）

![40个纯CSS绘制的图形37](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-facebook.png)

```
#facebook-icon {
  background: red;
  text-indent: -999em;
  width: 100px;
  height: 110px;
  border-radius: 5px;
  position: relative;
  overflow: hidden;
  border: 15px solid red;
  border-bottom: 0;
}
#facebook-icon::before {
  content: "/20";
  position: absolute;
  background: red;
  width: 40px;
  height: 90px;
  bottom: -30px;
  right: -37px;
  border: 20px solid #eee;
  border-radius: 25px;
}
#facebook-icon::after {
  content: "/20";
  position: absolute;
  width: 55px;
  top: 50px;
  height: 20px;
  background: #eee;
  right: 5px;
}
```

## Cone(圆锥形)

![40个纯CSS绘制的图形38](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-cone.png)

```
#cone {
  width: 0;
  height: 0;
  border-left: 70px solid transparent;
  border-right: 70px solid transparent;
  border-top: 100px solid red;
  -moz-border-radius: 50%;
  -webkit-border-radius: 50%;
  border-radius: 50%;
}
```

## Moon(月亮)

![40个纯CSS绘制的图形39](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-moon.png)

```
#moon {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  box-shadow: 15px 15px 0 0 red;
}
```

## Cross（十字架）

![40个纯CSS绘制的图形40](http://www.webhek.com/wordpress/wp-content/uploads/2016/10/shape-cross.png)

```
#cross {
  background: red;
  height: 100px;
  position: relative;
  width: 20px;
}
#cross:after {
  background: red;
  content: "";
  height: 20px;
  left: -40px;
  position: absolute;
  top: 40px;
  width: 100px;
}
```

以上就是纯CSS绘制的各种图形，感受到CSS的强大了吧。