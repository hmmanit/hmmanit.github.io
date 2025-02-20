---
layout: project
title:  Styleable Seek Bar
date:   2020-05-17 10:05:55 +0700
#image:  assets/images/blogs/kotlin_coroutines.webp
author: Man Ho
tags:   Android Development
github_url: https://github.com/homanad/StyleableSeekBar
---
[//]: # (---)

[//]: # (layout: project)

[//]: # (title: Styleable Seek Bar)

[//]: # (description: An seekbar allows creating many types of shape with many options.)

[//]: # (image:)

[//]: # (show_tile: false)

[//]: # (github_url: https://github.com/homanad/StyleableSeekBar)

[//]: # (---)

An seekbar allows creating many types of shape with many options.

- [Samples](#samples)
- [Gradle](#gradle)
- [Style](#style)
- [Orientation](#orientation)
- [Attributes](#attributes)
- [Usage](#usage)
    - [XML layout](#xml-layout)
    - [Programmatically](#programmatically)
- [License](#license)

## Samples
<img src="{% link assets/images/attachments/styleable_seek_bar/circular.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/rounded_rectangle.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/diamond.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/rectangle.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/triangle.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/hexagon.png %}" width="130" />

<img src="{% link assets/images/attachments/styleable_seek_bar/circular_vertical_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/rounded_rectangle_vertical_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/diamond_vertical_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/rectangle_vertical_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/triangle_vertical_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/hexagon_vertical_border.png %}" width="130" />

<img src="{% link assets/images/attachments/styleable_seek_bar/circular_gradient_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/rounded_rectangle_gradient_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/diamond_gradient_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/rectangle_gradient_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/triangle_gradient_border.png %}" width="130" /> <img src="{% link assets/images/attachments/styleable_seek_bar/hexagon_gradient_border.png %}" width="130" />

## Gradle

Step 1. Add this in your root build.gradle at the end of repositories:

    allprojects {
        repositories {
            ...
            maven { url 'https://www.jitpack.io' }
        }
    }

Step 2. Add dependency

    dependencies {
        ...
        implementation 'com.github.homanad:StyleableSeekBar:Tag'
    }

## Style

|Style | Value|
|------|------|
|CIRCULAR|0|
|ROUNDED_RECTANGLE|1|
|DIAMOND|2|
|RECTANGLE|3|
|TRIANGLE|4|
|HEXAGON|5|

## Orientation

|Orientation | Value|
|------|------|
|HORIZONTAL|0|
|VERTICAL|1|

## Attributes

For ease of use, you can also set all values in the corresponding code
as follows:

| Attribute      	| XML 					| Description 																|	Type 			| Default value	|
| ----------- 		| ----------- 			|----------- 																| -----------		|	-----------	|
| dotCount 		| sb_dotCount			| The number of dots equals 100% of the Seekbar     			|	Int		|	10			|
| style    		| sb_style			| The shape of each dot 	|	[Style](#style)		|	CIRCULAR		|
| roundedRadius  		| sb_rounded_radius			| This attribute is used exclusively for STYLE RECTANGLE, which is the rounded value of dot   									|	Dimension	|	10dp			|
| activeColor   		| sb_activeColor			| The color is shown for active dots   																|	ColorInt	|	Blue		|
| inactiveColor   	| sb_inactiveColor			|  The color is shown for inactive dots 										|	ColorInt			|	Gray		|
| minimumValue  		| sb_minimumValue			| The lowest number of dots that we must activate       			|	Int			|	stroke		|
| startColor   		| sb_progress_startColor			| For gradient progress, this is the starting color, combine with sb_progress_endColor to form a gradient range   |	ColorInt		|	Nothing			|
| endColor  | sb_progress_endColor	| For gradient progress, this is the starting color, combine with sb_progress_startColor to form a gradient range         							|	ColorInt		|	Nothing			|
| dotMargin  | sb_dotMargin	| The distance between dots         							|	Dimension		|	10dp			|
| selectedBorderColor  | sb_selected_borderColor	| Create a border for the active dot with this color        							|	ColorInt		|	Nothing			|
| selectedBorderWidth  | sb_selected_borderWidth	| Create a border for the active dot with this thickness         							|	Dimension		|	3dp			|
| orientation  | sb_orientation	| The direction of seek bar        							|	[Orientation](#orientation)		|	HORIZONTAL			|

## Usage

### XML layout

   	<com.homanad.android.widget.seekarc.StyleableSeekBar
        android:layout_width="100dp"
        android:layout_height="450dp"
        app:sb_activeColor="#239D28"
        app:sb_dotCount="10"
        app:sb_dotMargin="10dp"
        app:sb_inactiveColor="#545151"
        app:sb_minimumValue="1"
        app:sb_style="circular" />

### Programmatically

For ease of use, you can also set all values in the corresponding code
as follows:

    seekBar.dotCount = 10
    seekBar.style = StyleableSeekBar.HEXAGON
    seekBar.roundedRadius = 10.dp
    seekBar.activeColor = Color.BLUE
    seekBar.inactiveColor = Color.GRAY
    seekBar.minimumValue = 1
    seekBar.startColor = Color.CYAN
    seekBar.endColor = Color.YELLOW
    seekBar.dotMargin = 5.dp
    seekBar.selectedBorderColor = Color.RED
    seekBar.selectedBorderWidth = Color.parseColor("#64A4D6")
    seekBar.orientation = StyleableSeekBar.VERTICAL

Here, notice the values written with ".dp" extension

Because these attributes are dimension values, so for an Int or Float
value become a dp value, I've provided two extension functions to
convert the Int or Float type to dp, you just need to call it as an
extension function.

## License

```
Copyright 2021 Man Ho

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```