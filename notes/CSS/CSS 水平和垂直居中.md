Tags: #CSS

## 使用negative margins

```html
<div>Centered content</div>
```

```css
div {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 200px;
  height: 60px;
  line-height: 60px;
  margin-left: -100px;
  margin-top: -30px;
  text-align: center;
}
```

这个方法的缺点是：

1. 需要事先知道元素的大小，以便设置正确的负margin值。
2. 可能会影响整体布局。
3. 太复杂。

## 使用tanslations

```html
<div>Centered content</div>
```

```css
div {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
}
```

## 使用FlexBox

```html
<div>Centered content</div>
```

```css
div {
	dispaly: flex;
	align-items: center;
	justify-content: center;
}
```

## 使用Grid

```html
<div>Centered content</div>
```

```css
div {
	display: grid;
	align-items: center;
	justify-content: center;
}
```

or

```css
div {
	display: grid;
	place-items: center;
}
```