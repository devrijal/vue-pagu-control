# Vue PAGU Control

# Rendered vue result
![rendered components](https://github.com/devrijal/vue-pagu-control/blob/master/vue-pagu-control.png?raw=true)

- `pagu-control` backround changed regarding the percentage of used PAGU
	- yellow [`used_percentage < 90%`]
	- green [`used_percentage >= 90% && percentage <= 100%`]
	- red [`used_percentage > 100%`]

# Requirements

- [Vue.js](https://vuejs.org/) `^2.6.12`
- [Bootstrap](https://getbootstrap.com/) `^4.x`
- [Bootstrap-Vue](https://bootstrap-vue.org/) `^2.12.x`

# Properties
- *`get-url`* [`String`]
- *`post-url`* [`String`]
- *`get-cmd`* [`String`]
- *`post-cmd`* [`String`][optional]
- *`editable`* [`Boolean`] [available at `pagu-control` component only]


# Usage

```html
<div class="col-lg-5 col-md-5 col-sm-12 col-xs-12">
	<pagu-total get-url="telaah"></pagu-total>
</div>
<div class="col-lg-2 col-md-2 hidden-sm hidden-xs">

div>

<div class="col-lg-5 col-md-5 col-sm-12 col-xs-12">
	<pagu-control get-url="telaah" post-url="telaah" :editable="paguControlEditable"></pagu-control>
</div>
```

