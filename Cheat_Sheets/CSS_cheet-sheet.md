20210709_172935.jpg# CSS Cheet-Sheet :)

## CSS Selectors

### Universal Selector
Select all elements
```css
* 
```

### Type Selector
Select elements of that type
```css
h1 
```

### Class Selector
Select elements with that class
```css
.red
```

### ID Selector
Select elements with that id
```css
#red
```

## Combinators

### Descendant Selector
Select elements that are
descendants of the first
element
```css
ul li
```

## Direct Child Selector
Select elements that are
direct children of the first
element
```css
ul > li
```

## General Sibling Selector
Select elements that are
siblings of the first
element and are after it
```css
h1 ~ p
```

## Adjacent Sibling Selector
Select elements that are
siblings of the first
element and are immediately
after it
```css
h1 + p
```

## Or Selector
Select elements that are
either the first or the
second element
```css
h1, p
```

## And Selector
Select elements that are
both the first and the
second element
```css
h1.red
```

## Attribute Selector

## Has Attribute
Select elements that have
that attribute
```css
[a]
```

## Exact attribute
Select elements that have
that attribute with that
value
```css
[a="b"]
```

## Attribute starts with
Select elements that have
that attribute with a value
that starts with that value
```css
[a^="b"]
```

## Attribute ends with
Select elements that have
that attribute with a value
that ends with that value
```css
[a$="b"]
```

## Attribute contains
Select elements that have
that attribute with a value
that contains that value
```css
[a*="b"]
```

## Pseudo Elements

## Before
Creates an empty element
directly before the children of
selected element
```css
p::before
``` 

## After
Creates an empty element
directly after the children of
selected element
```css
p::after
```

## Pseudo Classes State

## Hover
Selects elements when the
mouse is over them
```css
a:hover
```

## Active
Selects elements when they
are being activated
```css
a:active
```

## Focus
Selects elements when they
are being focused. 
* Focus is set by either tabbing to an
element or clicking an element such as a
button or anchor tag
```css
a:focus
```

## Link
Selects elements that have
not been visited
```css
a:link
```

## Visited
Selects elements that have
been visited
```css
a:visited
```

## Required
Selects elements that are
required
```css
input:required
```

## Checked
Selects elements that are
checked
```css
input:checked
```

## Disabled
Selects elements that are
disabled
```css
input:disabled
```

## Optional
Selects elements that are
optional
```css
input:optional
```

## Valid
Selects elements that are
valid
```css
input:valid
```

## Pseudo Classes Position/Other

## First Child
Selects elements that are
the first child of their
parent
```css
p:first-child
```

## Last Child
Selects elements that are
the last child of their
parent
```css
p:last-child
```

## Nth Child
Selects elements that are
the nth child of their
parent
```css
p:nth-child(2)
```

## Nth Last Child
Selects elements that are
the nth last child of their
parent
```css
p:nth-last-child(2)
```

## Only Child
Selects elements that are
the only child of their
parent
```css  
p:only-child
```

## First of Type
Selects elements that are
the first of their type
```css
p:first-of-type
```

## Last of Type
Selects elements that are
the last of their type
```css
p:last-of-type
```

## Nth of Type
Selects elements that are
the nth of their type
```css
p:nth-of-type(2)
```

## Nth Last of Type
Selects elements that are
the nth last of their type
```css
p:nth-last-of-type(2)
```

## Only of Type
Selects elements that are
the only of their type
```css
p:only-of-type
```

## Not Selector
Selects elements that are
not the specified element
```css
a:not(.c)
```
----------------------------------------

## **Animations**

## Animation
Shorthand property for *animation-name* *animation-duration* *animation-timing-function* *animation-delay* *animation-iteration-count* *animation-direction* *animation-fill-mode* and *animation-play-state*.

Only animation-duration and animation-name are required.
```css
animation: name duration timing-function delay iteration-count direction fill-mode play-state;
```

### Animation Name
If no animation name is specified, no animation is played.
```css
animation-name: name;
```

### Animation Duration
Defines how long the animation lasts.
```css
animation-duration: time;
```

### Animation Timing Function
Defines how the values between the start and the end of the animation are calculated
```css
animation-timing-function: linear | ease | ease-in | ease-out | ease-in-out | cubic-bezier(n,n,n,n);
```

### Animation Delay
Defines how long the animation has to wait before starting. The animation will only be delayed on its first iteration.
```css
animation-delay: time;
```

### Animation Iteration Count
Defines how many times the animation is played.
```css
animation-iteration-count: number | infinite;
```

### Animation Direction
```css
animation-direction: normal | reverse | alternate | alternate-reverse;
```

### Animation Fill Mode
Defines what happens before an animation starts and after it ends. The fill mode allows to tell the browser if the animation’s styles should also be applied outside of the animation.
```css
animation-fill-mode: none | forwards | backwards | both;
```

### Animation Play State
If the animation-duration and animation-name are defined, the animation will start playing automatically.
```css
animation-play-state: running | paused;
```

-------------------------------------------------------------

## **Transitions**

## Transition
Shorthand property for *transition-property* *transition-duration* *transition-timing-function* and *transition-delay*.

Only transition-property and transition-duration are required.
```css
transition: property duration timing-function delay;
```

### Transition Property
Defines the property that will be animated.
```css
transition-property: property;
```

### Transition Duration
Defines how long the transition lasts.
```css
transition-duration: time;
```

### Transition Timing Function
Defines how the values between the start and the end of the transition are calculated.
```css
transition-timing-function: linear | ease | ease-in | ease-out | ease-in-out | cubic-bezier(n,n,n,n);
```

### Transition Delay
Defines how long the transition has to wait before starting.
```css
transition-delay: time;
```

-----------------------------------------------------------------

## Background

## Background Color
Sets the background color of an element.
```css
background-color: color;
```

## Background Image
Sets the background image of an element.
```css
background-image: url("image.jpg");
```

## Background Repeat
Sets how the background image repeats.
```css
background-repeat: repeat | repeat-x | repeat-y | no-repeat;
```

## Background Attachment
Sets whether the background image is fixed or scrolls with the rest of the page.
```css
background-attachment: scroll | fixed;
```

## Background Position
Sets the starting position of the background image.
```css
background-position: x y;
```

## Background Size
Sets the size of the background image.
```css
background-size: width height;
```

## Background Origin
Sets the origin position of the background image.
```css
background-origin: padding-box | border-box | content-box;
```

## Background Clip
Sets the clipping area of the background image.
```css
background-clip: border-box | padding-box | content-box;
```






