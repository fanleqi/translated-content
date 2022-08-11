---
title: Linear-gradient Generator
slug: Web/CSS/Tools/Linear-gradient_Generator
tags:
  - CSS
  - Werkzeuge
translation_of: Web/CSS/Tools/Linear-gradient_Generator
---
```html hidden
    <div id="container">
        <div id="gradient-container" data-alpha="true">
        </div>

        <div id="controls">
            <div class="section">
                <div class="title"> Active point </div>
                <div class="property">
                <div class="ui-input-slider" data-topic="point-position" data-info="position"
                    data-unit="px" data-min="-1000" data-value="0" data-max="1000" data-sensivity="5"></div>
                    <div id="delete-point" class="button"> delete point </div>
                </div>
                <div class="ui-color-picker" data-topic="picker"></div>
            </div>

            <div class="section">
                <div class="title"> Active axis </div>
                <div class="property">
                    <div class="name"> axis unit </div>
                    <div class="ui-dropdown" data-topic="axis-unit" data-selected="1">
                        <div data-value='px'> pixels (px) </div>
                        <div data-value='%'> percentage (%) </div>
                    </div>
                    <div id="delete-axis" class="button"> delete line </div>
                </div>
                <div class="property">
                    <div class="ui-slider" data-topic="axis-rotation" data-info="rotation"
                        data-min="-180" data-value="0" data-max="180"></div>
                </div>
            </div>

            <div id="tool-section" class="section">
                <div class="title"> Tool settings </div>
                <div class="property">
                    <div class="name"> alpha background </div>
                    <div id="canvas-bg"></div>
                    <div id="add-axis" class="button"> add line </div>
                </div>
                <div id="order">
                    <div id="gradient-axes"></div>
                    <div id="gradient-order"></div>
                </div>
            </div>
        </div>

        <div id="output">
            <div class="css-property">
                <span class="property">background:</span>
                <span class="value"></span>
            </div>
        </div>
    </div>
```

```css hidden
/*
 * COLOR PICKER TOOL
 */

.ui-color-picker {
	width: 420px;
	margin: 0;
	border: 1px solid #DDD;
	background-color: #FFF;
	display: table;

	-moz-user-select: none;
	-webkit-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

.ui-color-picker .picking-area {
	width: 198px;
	height: 198px;
	margin: 5px;
	border: 1px solid #DDD;
	position: relative;
	float: left;
	display: table;
}

.ui-color-picker .picking-area:hover {
	cursor: default;
}

/* HSV format - Hue-Saturation-Value(Brightness) */
.ui-color-picker .picking-area {
	background: url("images/picker_mask.png");

	background: -moz-linear-gradient(bottom, #000 0%, rgba(0, 0, 0, 0) 100%),
				-moz-linear-gradient(left, #FFF 0%, rgba(255, 255, 255, 0) 100%);
	background: -webkit-linear-gradient(bottom, #000 0%, rgba(0, 0, 0, 0) 100%),
				-webkit-linear-gradient(left, #FFF 0%, rgba(255, 255, 255, 0) 100%);
	background: -ms-linear-gradient(bottom, #000 0%, rgba(0, 0, 0, 0) 100%),
				-ms-linear-gradient(left, #FFF 0%, rgba(255, 255, 255, 0) 100%);
	background: -o-linear-gradient(bottom, #000 0%, rgba(0, 0, 0, 0) 100%),
				-o-linear-gradient(left, #FFF 0%, rgba(255, 255, 255, 0) 100%);

	background-color: #F00;
}

/* HSL format - Hue-Saturation-Lightness */
.ui-color-picker[data-mode='HSL'] .picking-area {
	background: -moz-linear-gradient(top, hsl(0, 0%, 100%) 0%, hsla(0, 0%, 100%, 0) 50%,
									hsla(0, 0%, 0%, 0) 50%, hsl(0, 0%, 0%) 100%),
				-moz-linear-gradient(left, hsl(0, 0%, 50%) 0%, hsla(0, 0%, 50%, 0) 100%);
	background: -webkit-linear-gradient(top, hsl(0, 0%, 100%) 0%, hsla(0, 0%, 100%, 0) 50%,
									hsla(0, 0%, 0%, 0) 50%, hsl(0, 0%, 0%) 100%),
				-webkit-linear-gradient(left, hsl(0, 0%, 50%) 0%, hsla(0, 0%, 50%, 0) 100%);
	background: -ms-linear-gradient(top, hsl(0, 0%, 100%) 0%, hsla(0, 0%, 100%, 0) 50%,
									hsla(0, 0%, 0%, 0) 50%, hsl(0, 0%, 0%) 100%),
				-ms-linear-gradient(left, hsl(0, 0%, 50%) 0%, hsla(0, 0%, 50%, 0) 100%);
	background: -o-linear-gradient(top, hsl(0, 0%, 100%) 0%, hsla(0, 0%, 100%, 0) 50%,
									hsla(0, 0%, 0%, 0) 50%, hsl(0, 0%, 0%) 100%),
				-o-linear-gradient(left, hsl(0, 0%, 50%) 0%, hsla(0, 0%, 50%, 0) 100%);
	background-color: #F00;
}

.ui-color-picker .picker {
	width: 10px;
	height: 10px;
	margin: -5px 0 0 -5px;
	border-radius: 50%;
	border: 1px solid #FFF;
	position: absolute;
	top: 45%;
	left: 45%;
}

.ui-color-picker .picker:before {
	width: 8px;
	height: 8px;
	content: "";
	position: absolute;
	border: 1px solid #999;
	border-radius: 50%;
}

.ui-color-picker .hue,
.ui-color-picker .alpha {
	width: 198px;
	height: 28px;
	margin: 5px;
	border: 1px solid #FFF;
	float: left;
}

.ui-color-picker .hue {
	background: url("images/hue_mask.png");
	background: -moz-linear-gradient(left, #F00 0%, #FF0 16.66%, #0F0 33.33%, #0FF 50%,
				#00F 66.66%, #F0F 83.33%, #F00 100%);
	background: -webkit-linear-gradient(left, #F00 0%, #FF0 16.66%, #0F0 33.33%, #0FF 50%,
				#00F 66.66%, #F0F 83.33%, #F00 100%);
	background: -ms-linear-gradient(left, #F00 0%, #FF0 16.66%, #0F0 33.33%, #0FF 50%,
				#00F 66.66%, #F0F 83.33%, #F00 100%);
	background: -o-linear-gradient(left, #F00 0%, #FF0 16.66%, #0F0 33.33%, #0FF 50%,
				#00F 66.66%, #F0F 83.33%, #F00 100%);
}

.ui-color-picker .alpha {
	border: 1px solid #CCC;
	background: url("images/alpha_mask.png");
}

.ui-color-picker .slider-picker {
	width: 2px;
	height: 100%;
	margin: 0 0 0 -2px;
	border: 1px solid #777;
	background-color: #FFF;
	position: relative;
	top: -1px;
}

/* input HSV and RGB */

.ui-color-picker .info {
	width: 200px;
	margin: 5px;
	float: left;
}

.ui-color-picker .info * {
	float: left;
}

.ui-color-picker .input {
	width: 64px;
	margin: 5px 2px;
	float: left;
}

.ui-color-picker .input .name {
	height: 20px;
	width: 30px;
	text-align: center;
	font-size: 14px;
	line-height: 18px;
	float: left;
}

.ui-color-picker .input input {
	width: 30px;
	height: 18px;
	margin: 0;
	padding: 0;
	border: 1px solid #DDD;
	text-align: center;
	float: right;

	-moz-user-select: text;
	-webkit-user-select: text;
	-ms-user-select: text;
}

.ui-color-picker .input[data-topic="lightness"] {
	display: none;
}

.ui-color-picker[data-mode='HSL'] .input[data-topic="value"] {
	display: none;
}

.ui-color-picker[data-mode='HSL'] .input[data-topic="lightness"] {
	display: block;
}

.ui-color-picker .input[data-topic="alpha"] {
	margin-top: 10px;
	width: 93px;
}

.ui-color-picker .input[data-topic="alpha"] > .name {
	width: 60px;
}

.ui-color-picker .input[data-topic="alpha"] > input {
	float: right;
}

.ui-color-picker .input[data-topic="hexa"] {
	width: auto;
	float: right;
	margin: 6px 8px 0 0;
}

.ui-color-picker .input[data-topic="hexa"] > .name {
	display: none;
}

.ui-color-picker .input[data-topic="hexa"] > input {
	width: 90px;
	height: 24px;
	padding: 2px 0;
	-moz-box-sizing: border-box;
	-webkit-box-sizing: border-box;
	box-sizing: border-box;
}

/* Preview color */
.ui-color-picker .preview {
	width: 95px;
	height: 53px;
	margin: 5px;
	margin-top: 10px;
	border: 1px solid #DDD;
	background-image: url("images/alpha.png");
	float: left;
	position: relative;
}

.ui-color-picker .preview:before {
	height: 100%;
	width: 50%;
	left: 50%;
	top: 0;
	content: "";
	background: #FFF;
	position: absolute;
	z-index: 1;
}

.ui-color-picker .preview-color {
	width: 100%;
	height: 100%;
	background-color: rgba(255, 0, 0, 0.5);
	position: absolute;
	z-index: 1;
}

.ui-color-picker .switch_mode {
	width: 10px;
	height: 20px;
	position: relative;
	border-radius: 5px 0 0 5px;
	border: 1px solid #DDD;
	background-color: #EEE;
	left: -12px;
	top: -1px;
	z-index: 1;
	transition: all 0.5s;
}

.ui-color-picker .switch_mode:hover {
	background-color: #CCC;
	cursor: pointer;
}

/*
 * UI Component
 */

.ui-input-slider {
	height: 20px;
	font-family: "Segoe UI", Arial, Helvetica, sans-serif;
	-moz-user-select: none;
	user-select: none;
}

.ui-input-slider * {
	float: left;
	height: 100%;
}

/* Input Slider */

.ui-input-slider > input {
	margin: 0;
	padding: 0;
	width: 50px;
	text-align: center;

	-moz-box-sizing: border-box;
	-webkit-box-sizing: border-box;
	box-sizing: border-box;
}

.ui-input-slider-info {
	width: 90px;
	padding: 0px 10px 0px 0px;
	text-align: right;
	text-transform: lowercase;
	line-height: inherit;
}

.ui-input-slider-left, .ui-input-slider-right {
	width: 16px;
	cursor: pointer;
	background: url("arrows.png") center left no-repeat;
}

.ui-input-slider-right {
	background: url("arrows.png") center right no-repeat;
}

.ui-input-slider-name {
	width: 90px;
	padding: 0 10px 0 0;
	text-align: right;
	text-transform: lowercase;
}

.ui-input-slider-btn-set {
	width: 25px;
	background-color: #2C9FC9;
	border-radius: 5px;
	color: #FFF;
	font-weight: bold;
	line-height: 14px;
	text-align: center;
}

.ui-input-slider-btn-set:hover {
	background-color: #379B4A;
	cursor: pointer;
}

/*
 * UI Slider
 */

.ui-slider {
	height: 20px;
	margin: 10px 0;
	font-family: "Segoe UI", Arial, Helvetica, sans-serif;
	-moz-user-select: none;
	user-select: none;
}

.ui-slider > * {
	float: left;
	height: 100%;
	line-height: 100%;
}

/* Slider */

.ui-slider-slider {
	height: 10px;
	width: 200px;
	margin: 4px 10px;
	display: block;
	border: 1px solid #999;
	border-radius: 3px;
	background: #EEE;
}

.ui-slider-slider:hover {
	cursor: pointer;
}

.ui-slider-name {
	width: 90px;
	padding: 0 10px 0 0;
	text-align: right;
	text-transform: lowercase;
}

.ui-slider-pointer {
	width: 12px;
	height: 13px;
	margin: 0 0 0 -7px;
	background-color: #EEE;
	border: 1px solid #2C9FC9;
	border-radius: 3px;
	position: relative;
	top: -3px;
	left: 0%;
}

.ui-slider-button {
	width: 25px;
	background-color: #2C9FC9;
	border-radius: 3px;
	color: #FFF;
	font-weight: bold;
	line-height: 14px;
	text-align: center;
}

.ui-slider-button:hover {
	background-color: #379B4A;
	cursor: pointer;
}

.ui-slider > input {
	width: 50px;
	margin: 0 10px;
	padding: 0;
	text-align: center;
}

/*
 * UI DropDown
 */

/* Dropdown */

.ui-dropdown {
	height: 2em;
	width: 120px;
	font-family: "Segoe UI", Arial, Helvetica, sans-serif;
	font-size: 12px;

	background-image: url("dropdown_arrow.svg");
	background-position: right 0.3em center;
	background-repeat: no-repeat;
	background-color: #359740;
	background-size: 1.1em;

	position: relative;

	-moz-box-sizing: border-box;
	-webkit-box-sizing: border-box;
	box-sizing: border-box;

	-moz-user-select: none;
	-webkit-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

.ui-dropdown:hover {
	cursor: pointer;
	background-color: #208B20;
}

/* Dropdown Select Button */

.ui-dropdown-select {
	padding: 0 0.75em;
	color: #FFF;
	line-height: 2em;
}

/* Dropdown List */

.ui-dropdown-list {
	width: 100%;
	height: 150px;
	max-height: 150px;
	margin: 0;
	padding: 0 0.3em;

	border: 1px solid #3490D2;
	border-color: #208B20;
	background: #666;
	background-color: #EEF1F5;
	color: #000;

	position: absolute;
	top: 100%;
	left: 0;
	z-index: 100;

	overflow: hidden;
	transition: all 0.3s;

	-moz-box-sizing: border-box;
	-webkit-box-sizing: border-box;
	box-sizing: border-box;
}

.ui-dropdown-list:hover {
	overflow: auto;
}

.ui-dropdown-list[data-hidden='true'] {
	height: 0 !important;
	opacity: 0;
	visibility: hidden;
}

.ui-dropdown[data-position='left'] .ui-dropdown-list {
	left: -100%;
	top: 0;
}

.ui-dropdown[data-position='right'] .ui-dropdown-list {
	left: 100%;
	top: 0;
}

.ui-dropdown-list > div {
	width: 80%;
	height: 1.6em;
	margin: 0.3em 0;
	padding: 0.3em 10%;
	line-height: 1.6em;
}

.ui-dropdown-list > div:hover {
	background: #3490D2;
	color:#FFF;
	border-radius: 2px;
	cursor: pointer;
}

/*
 * COLOR PICKER TOOL
 */

body {
	width: 100%;
	height: 100%;
	margin: 0 auto;

	font-family: "Segoe UI", Arial, Helvetica, sans-serif;

	-moz-user-select: none;
	-webkit-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

body[data-dragging="true"] {
}

/**
 * Resize Handle
 */

.resize-handle {
	width: 10px;
	height: 10px;
	background: url("images/resize.png") center center no-repeat;
	position: absolute;
	bottom: 0;
	right: 0;
}

[data-resize='both']:hover {
	cursor: nw-resize !important;
}

[data-resize='width']:hover {
	cursor: w-resize !important;
}

[data-resize='height']:hover {
	cursor: n-resize !important;
}

/**
 * Main Container
 */

#container {
	width: 1000px;
	height: 100%;
	display: table;
	margin: 0 auto;
}


#gradient-container {
	width: 450px;
	height: 250px;
	min-width: 8px;
	min-height: 8px;
	margin: 100px auto;
	border: 1px solid #DDD;
	position: relative;
}

#gradient-container[data-alpha="true"]:after {
	content: "";
	width: 100%;
	height: 100%;
	background: url('images/canvas-pattern.png');
	position: absolute;
	z-index: -1;
}

/**
 * 	Gradient Axis
 */

.gradient-axis {
	width: 125%;
	height: 4px;
	margin: -3px 0 0 0;
	border: 1px solid #CCC;
	position: absolute;
	top: 50%;
	left: 0;
	opacity: 0.5;
}

.gradient-axis[data-active='true'] {
	opacity: 1;
	z-index: 1;
}

.gradient-axis:after {
	content: "";
	width: 25px;
	height: 100%;
	background-color: #CCC;
	position: absolute;
	left: 0;
	top: 0;
}

.gradient-axis .gradient-line {
	width: 80%;
	height: 100%;
	margin: 0px auto;
	position: relative;
}

.gradient-axis .gradient-line:hover {
	cursor: pointer;
}

.gradient-axis .gradient-line:after,
.gradient-axis .gradient-line:before {
	content: "";
	width: 1px;
	height: 100px;
	background-color: #CCC;
	position: absolute;
	top: -48px;
}

.gradient-axis .gradient-line:after {
	left: -1px;
}

.gradient-axis .gradient-line:before {
	right: -1px;
}

.gradient-axis .gradient-point {
	width: 16px;
	height: 16px;
	margin: 0 0 0 -8px;
	top: -7px;
	border-radius: 50%;
	background-color: #FFF;
	border: 1px solid #999;
	position: absolute;
	z-index: 1;
}

.gradient-axis .gradient-point:after {
	content: "";
	width: 6px;
	height: 6px;
	border-radius: 50%;
	background-color: #FFF;
	border: 1px solid #999;
	position: absolute;
	top: 4px;
	left: 4px;
}

.gradient-axis .gradient-point:hover:after,
.gradient-axis .gradient-point[data-active="true"]:after {
	background-color: #CCC;
}

.gradient-axis .rotate-point {
	width: 25px;
	height: 25px;
	position: absolute;
	top: -10.5px;
	right: -12px;
}

.gradient-axis[axisID='0']:after {
	background-color: #DA5C5C;
}

.gradient-axis[axisID='1']:after {
	background-color: #5CDA9B;
}

.gradient-axis[axisID='2']:after {
	background-color: #5C9BDA;
}

.gradient-axis[axisID='3']:after {
	background-color: #5C5CDA;
}

.gradient-axis[axisID='0'] path {
	fill: #DA5C5C;
}

.gradient-axis[axisID='1'] path {
	fill: #5CDA9B;
}

.gradient-axis[axisID='2'] path {
	fill: #5C9BDA;
}

.gradient-axis[axisID='3'] path {
	fill: #5C5CDA;
}


.gradient-axis .rotate-point:hover {
	cursor: pointer;
}

/**
 * Controls
 */

#controls {
	width: 100%;
	margin: 0 auto;
	display: table;
}

#controls .section {
	width: 50%;
	padding: 10px;
	display: table;
	float: left;

	-moz-box-sizing: border-box;
	-webkit-box-sizing: border-box;
	box-sizing: border-box;
}

#controls .section .title {
	width: 90%;
	margin: 0 0 10px 0;
	padding: 5px;
	border-bottom: 1px solid #DDD;
	font-size: 18px;
	color: #777;
}

#controls .property {
	width: 100%;
	height: 24px;
	margin: 10px 0;
	padding: 3px 0;
	display: table;
}

#controls .property > * {
	float: left;
}

#controls .property .name {
	width: 90px;
	padding: 0px 10px 0px 0px;
	text-align: right;
	line-height: 150%;
}

/* Button */

#controls .button {
	height: 24px;
	padding: 0 10px;
	background-color: #379B4A;
	border-radius: 3px;
	font-size: 14px;
	color: #FFF;
	display: inline;
	float: left;
}

#controls .button[data-state='disabled'] {
	background-color: #CCC !important;
	color: #777 !important;
}

#controls .button[data-state='disabled']:hover {
	background-color: #CCC !important;
	cursor: default !important;
}

#controls .button:hover {
	cursor: pointer;
	background-color: #208B20;
}

/* Active Point  */

.ui-input-slider {
	height: 24px;
	line-height: 20px;
}

#delete-point {
	margin: 0 58px 0 0;
	float: right !important;
}

#controls .ui-color-picker[data-topic="picker"] {
	margin: 20px 0 0 0;
}

#controls .ui-input-slider[data-topic="axis-rotation"] {
}

#controls .ui-dropdown {
	width: 130px;
	height: 24px;
}

#controls .ui-dropdown-select {
	line-height: 24px;
}

#controls .ui-dropdown-list {
	height: 66px;
	line-height: 2.5em;
	overflow: hidden;
}

#delete-axis {
	margin: 0 38px 0 0;
	float: right !important;
}

/* Tool controls */

#tool-section .property .name {
	width: 150px;
}

#canvas-bg {
	width: 16px;
	height: 16px;
	margin: 5px;
	background: url("images/toggle-background.png") center right no-repeat;
}

#canvas-bg:hover {
	cursor: pointer;
}

#canvas-bg[data-alpha='false'] {
	background-position: center left;
}

#canvas-bg[data-alpha='true'] {
	background-position: center right;
}

/* Order gradients */

#order {
	margin-left: 24px;
}

#gradient-axes {
	width: 100%;
	height: 30px;
	padding: 0 0 0 15px;
	display: table;
	position: relative;
}

#gradient-axes .axis {
	width: 50px;
	height: 20px;
	margin: 5px 0;
	background-color: #DDD;
	text-align: center;
	float: left;
	transition: all 0.3s;
	position: absolute;
}

#gradient-axes .axis:hover {
	margin: 2px 0;
	height: 26px;
	background-color: #CCC;
	cursor: pointer;
}

#gradient-axes .axis[data-state='active'] {
	margin: 2px 0;
	height: 26px;
}

#gradient-axes .axis[data-state='active']:after {
	content: "*";
	color: #FFF;
	padding: 3px;
}

#gradient-axes .axis[axisID='0'] {
	background-color: #DA5C5C;
}

#gradient-axes .axis[axisID='1'] {
	background-color: #5CDA9B;
}

#gradient-axes .axis[axisID='2'] {
	background-color: #5C9BDA;
}

#gradient-axes .axis[axisID='3'] {
	background-color: #5C5CDA;
}

#add-axis.button {
	margin: 0 38px 0 0;
	float: right;
}

#gradient-order {
	width: 60%;
	height: 5px;
	margin: 5px 0 0 0;
	overflow: visible;
	background-color: #CCC;
	position: relative;
}

#gradient-order:before {
	content: "gradient order - drag to order";
	position: absolute;
	top: 100%;
	color: #CCC;
}

#gradient-order:after {
	content: "";
	width: 24px;
	height: 24px;
	background: url('images/arrow.svg');
	background-size: 24px 24px;
	position: absolute;
	right: -10px;
	top: -9.5px;
}


/**
 * Output
 */

#output {
	width: 100%;
	padding: 10px 0;
	margin: 20px 0 50px 0;
	border: 2px dashed #CCC;
	border-radius: 5px;
	display: table;
	font-size: 13px;
	overflow: hidden;

	-moz-user-select: text;
	-webkit-user-select: text;
	-ms-user-select: text;
	user-select: text;
}

#output .css-property {
	width: 90%;
	margin: 5px 5%;
	color: #777;
	position: relative;
	float: left;
}

#output .property {
	height: 100%;
	width: 12%;
	position: absolute;
	left: 0;
}

#output .value {
	width: 88%;
	position: absolute;
	white-space: pre;
	word-wrap: break-word;
	display: block;
	right: 0;

}
```

```js hidden
var UIColorPicker = (function UIColorPicker() {
	'use strict';

	function getElemById(id) {
		return document.getElementById(id);
	}

	var subscribers = [];
	var pickers = [];

	/**
	 * RGBA Color class
	 *
	 * HSV/HSB and HSL (hue, saturation, value / brightness, lightness)
	 * @param hue			0-360
	 * @param saturation	0-100
	 * @param value 		0-100
	 * @param lightness		0-100
	 */

	function Color(color) {

		if(color instanceof Color === true) {
			this.copy(color);
			return;
		}

		this.r = 0;
		this.g = 0;
		this.b = 0;
		this.a = 1;
		this.hue = 0;
		this.saturation = 0;
		this.value = 0;
		this.lightness = 0;
		this.format = 'HSV';
	}

	function RGBColor(r, g, b) {
		var color = new Color();
		color.setRGBA(r, g, b, 1);
		return color;
	}

	function RGBAColor(r, g, b, a) {
		var color = new Color();
		color.setRGBA(r, g, b, a);
		return color;
	}

	function HSVColor(h, s, v) {
		var color = new Color();
		color.setHSV(h, s, v);
		return color;
	}

	function HSVAColor(h, s, v, a) {
		var color = new Color();
		color.setHSV(h, s, v);
		color.a = a;
		return color;
	}

	function HSLColor(h, s, l) {
		var color = new Color();
		color.setHSL(h, s, l);
		return color;
	}

	function HSLAColor(h, s, l, a) {
		var color = new Color();
		color.setHSL(h, s, l);
		color.a = a;
		return color;
	}

	Color.prototype.copy = function copy(obj) {
		if(obj instanceof Color !== true) {
			console.log('Typeof parameter not Color');
			return;
		}

		this.r = obj.r;
		this.g = obj.g;
		this.b = obj.b;
		this.a = obj.a;
		this.hue = obj.hue;
		this.saturation = obj.saturation;
		this.value = obj.value;
		this.format = '' + obj.format;
		this.lightness = obj.lightness;
	};

	Color.prototype.setFormat = function setFormat(format) {
		if (format === 'HSV')
			this.format = 'HSV';
		if (format === 'HSL')
			this.format = 'HSL';
	};

	/*========== Methods to set Color Properties ==========*/

	Color.prototype.isValidRGBValue = function isValidRGBValue(value) {
		return (typeof(value) === 'number' && isNaN(value) === false &&
			value >= 0 && value <= 255);
	};

	Color.prototype.setRGBA = function setRGBA(red, green, blue, alpha) {
		if (this.isValidRGBValue(red) === false ||
			this.isValidRGBValue(green) === false ||
			this.isValidRGBValue(blue) === false)
			return;

			this.r = red | 0;
			this.g = green | 0;
			this.b = blue | 0;

		if (this.isValidRGBValue(alpha) === true)
			this.a = alpha | 0;
	};

	Color.prototype.setByName = function setByName(name, value) {
		if (name === 'r' || name === 'g' || name === 'b') {
			if(this.isValidRGBValue(value) === false)
				return;

			this[name] = value;
			this.updateHSX();
		}
	};

	Color.prototype.setHSV = function setHSV(hue, saturation, value) {
		this.hue = hue;
		this.saturation = saturation;
		this.value = value;
		this.HSVtoRGB();
	};

	Color.prototype.setHSL = function setHSL(hue, saturation, lightness) {
		this.hue = hue;
		this.saturation = saturation;
		this.lightness = lightness;
		this.HSLtoRGB();
	};

	Color.prototype.setHue = function setHue(value) {
		if (typeof(value) !== 'number' || isNaN(value) === true ||
			value < 0 || value > 359)
			return;
		this.hue = value;
		this.updateRGB();
	};

	Color.prototype.setSaturation = function setSaturation(value) {
		if (typeof(value) !== 'number' || isNaN(value) === true ||
			value < 0 || value > 100)
			return;
		this.saturation = value;
		this.updateRGB();
	};

	Color.prototype.setValue = function setValue(value) {
		if (typeof(value) !== 'number' || isNaN(value) === true ||
			value < 0 || value > 100)
			return;
		this.value = value;
		this.HSVtoRGB();
	};

	Color.prototype.setLightness = function setLightness(value) {
		if (typeof(value) !== 'number' || isNaN(value) === true ||
			value < 0 || value > 100)
			return;
		this.lightness = value;
		this.HSLtoRGB();
	};

	Color.prototype.setAlpha = function setAlpha(value) {
		if (typeof(value) !== 'number' || isNaN(value) === true ||
			value < 0 || value > 1)
			return;
		this.a = parseFloat(value.toFixed(2));
	};

	Color.prototype.setHexa = function setHexa(value) {
		var valid  = /(^#{0,1}[0-9A-F]{6}$)|(^#{0,1}[0-9A-F]{3}$)/i.test(value);

		if (valid !== true)
			return;

		if (value[0] === '#')
			value = value.slice(1, value.length);

		if (value.length === 3)
			value = value.replace(/([0-9A-F])([0-9A-F])([0-9A-F])/i,'$1$1$2$2$3$3');

		this.r = parseInt(value.substr(0, 2), 16);
		this.g = parseInt(value.substr(2, 2), 16);
		this.b = parseInt(value.substr(4, 2), 16);

		this.alpha	= 1;
		this.RGBtoHSV();
	};

	/*========== Conversion Methods ==========*/

	Color.prototype.convertToHSL = function convertToHSL() {
		if (this.format === 'HSL')
			return;

		this.setFormat('HSL');
		this.RGBtoHSL();
	};

	Color.prototype.convertToHSV = function convertToHSV() {
		if (this.format === 'HSV')
			return;

		this.setFormat('HSV');
		this.RGBtoHSV();
	};

	/*========== Update Methods ==========*/

	Color.prototype.updateRGB = function updateRGB() {
		if (this.format === 'HSV') {
			this.HSVtoRGB();
			return;
		}

		if (this.format === 'HSL') {
			this.HSLtoRGB();
			return;
		}
	};

	Color.prototype.updateHSX = function updateHSX() {
		if (this.format === 'HSV') {
			this.RGBtoHSV();
			return;
		}

		if (this.format === 'HSL') {
			this.RGBtoHSL();
			return;
		}
	};

	Color.prototype.HSVtoRGB = function HSVtoRGB() {
		var sat = this.saturation / 100;
		var value = this.value / 100;
		var C = sat * value;
		var H = this.hue / 60;
		var X = C * (1 - Math.abs(H % 2 - 1));
		var m = value - C;
		var precision = 255;

		C = (C + m) * precision | 0;
		X = (X + m) * precision | 0;
		m = m * precision | 0;

		if (H >= 0 && H < 1) {	this.setRGBA(C, X, m);	return; }
		if (H >= 1 && H < 2) {	this.setRGBA(X, C, m);	return; }
		if (H >= 2 && H < 3) {	this.setRGBA(m, C, X);	return; }
		if (H >= 3 && H < 4) {	this.setRGBA(m, X, C);	return; }
		if (H >= 4 && H < 5) {	this.setRGBA(X, m, C);	return; }
		if (H >= 5 && H < 6) {	this.setRGBA(C, m, X);	return; }
	};

	Color.prototype.HSLtoRGB = function HSLtoRGB() {
		var sat = this.saturation / 100;
		var light = this.lightness / 100;
		var C = sat * (1 - Math.abs(2 * light - 1));
		var H = this.hue / 60;
		var X = C * (1 - Math.abs(H % 2 - 1));
		var m = light - C/2;
		var precision = 255;

		C = (C + m) * precision | 0;
		X = (X + m) * precision | 0;
		m = m * precision | 0;

		if (H >= 0 && H < 1) {	this.setRGBA(C, X, m);	return; }
		if (H >= 1 && H < 2) {	this.setRGBA(X, C, m);	return; }
		if (H >= 2 && H < 3) {	this.setRGBA(m, C, X);	return; }
		if (H >= 3 && H < 4) {	this.setRGBA(m, X, C);	return; }
		if (H >= 4 && H < 5) {	this.setRGBA(X, m, C);	return; }
		if (H >= 5 && H < 6) {	this.setRGBA(C, m, X);	return; }
	};

	Color.prototype.RGBtoHSV = function RGBtoHSV() {
		var red		= this.r / 255;
		var green	= this.g / 255;
		var blue	= this.b / 255;

		var cmax = Math.max(red, green, blue);
		var cmin = Math.min(red, green, blue);
		var delta = cmax - cmin;
		var hue = 0;
		var saturation = 0;

		if (delta) {
			if (cmax === red ) { hue = ((green - blue) / delta); }
			if (cmax === green ) { hue = 2 + (blue - red) / delta; }
			if (cmax === blue ) { hue = 4 + (red - green) / delta; }
			if (cmax) saturation = delta / cmax;
		}

		this.hue = 60 * hue | 0;
		if (this.hue < 0) this.hue += 360;
		this.saturation = (saturation * 100) | 0;
		this.value = (cmax * 100) | 0;
	};

	Color.prototype.RGBtoHSL = function RGBtoHSL() {
		var red		= this.r / 255;
		var green	= this.g / 255;
		var blue	= this.b / 255;

		var cmax = Math.max(red, green, blue);
		var cmin = Math.min(red, green, blue);
		var delta = cmax - cmin;
		var hue = 0;
		var saturation = 0;
		var lightness = (cmax + cmin) / 2;
		var X = (1 - Math.abs(2 * lightness - 1));

		if (delta) {
			if (cmax === red ) { hue = ((green - blue) / delta); }
			if (cmax === green ) { hue = 2 + (blue - red) / delta; }
			if (cmax === blue ) { hue = 4 + (red - green) / delta; }
			if (cmax) saturation = delta / X;
		}

		this.hue = 60 * hue | 0;
		if (this.hue < 0) this.hue += 360;
		this.saturation = (saturation * 100) | 0;
		this.lightness = (lightness * 100) | 0;
	};

	/*========== Get Methods ==========*/

	Color.prototype.getHexa = function getHexa() {
		var r = this.r.toString(16);
		var g = this.g.toString(16);
		var b = this.b.toString(16);
		if (this.r < 16) r = '0' + r;
		if (this.g < 16) g = '0' + g;
		if (this.b < 16) b = '0' + b;
		var value = '#' + r + g + b;
		return value.toUpperCase();
	};

	Color.prototype.getRGBA = function getRGBA() {

		var rgb = '(' + this.r + ', ' + this.g + ', ' + this.b;
		var a = '';
		var v = '';
		var x = parseFloat(this.a);
		if (x !== 1) {
			a = 'a';
			v = ', ' + x;
		}

		var value = 'rgb' + a + rgb + v + ')';
		return value;
	};

	Color.prototype.getHSLA = function getHSLA() {
		if (this.format === 'HSV') {
			var color = new Color(this);
			color.setFormat('HSL');
			color.updateHSX();
			return color.getHSLA();
		}

		var a = '';
		var v = '';
		var hsl = '(' + this.hue + ', ' + this.saturation + '%, ' + this.lightness +'%';
		var x = parseFloat(this.a);
		if (x !== 1) {
			a = 'a';
			v = ', ' + x;
		}

		var value = 'hsl' + a + hsl + v + ')';
		return value;
	};

	Color.prototype.getColor = function getColor() {
		if (this.a | 0 === 1)
			return this.getHexa();
		return this.getRGBA();
	};

	/*=======================================================================*/
	/*=======================================================================*/

	/*========== Capture Mouse Movement ==========*/

	var setMouseTracking = function setMouseTracking(elem, callback) {
		elem.addEventListener('mousedown', function(e) {
			callback(e);
			document.addEventListener('mousemove', callback);
		});

		document.addEventListener('mouseup', function(e) {
			document.removeEventListener('mousemove', callback);
		});
	};

	/*====================*/
	// Color Picker Class
	/*====================*/

	function ColorPicker(node) {
		this.color = new Color();
		this.node = node;
		this.subscribers = [];

		var type = this.node.getAttribute('data-mode');
		var topic = this.node.getAttribute('data-topic');

		this.topic = topic;
		this.picker_mode = (type === 'HSL') ? 'HSL' : 'HSV';
		this.color.setFormat(this.picker_mode);

		this.createPickingArea();
		this.createHueArea();

		this.newInputComponent('H', 'hue', this.inputChangeHue.bind(this));
		this.newInputComponent('S', 'saturation', this.inputChangeSaturation.bind(this));
		this.newInputComponent('V', 'value', this.inputChangeValue.bind(this));
		this.newInputComponent('L', 'lightness', this.inputChangeLightness.bind(this));

		this.createAlphaArea();

		this.newInputComponent('R', 'red', this.inputChangeRed.bind(this));
		this.newInputComponent('G', 'green', this.inputChangeGreen.bind(this));
		this.newInputComponent('B', 'blue', this.inputChangeBlue.bind(this));

		this.createPreviewBox();
		this.createChangeModeButton();

		this.newInputComponent('alpha', 'alpha', this.inputChangeAlpha.bind(this));
		this.newInputComponent('hexa', 'hexa', this.inputChangeHexa.bind(this));

		this.setColor(this.color);
		pickers[topic] = this;
	}

	/*************************************************************************/
	//				Function for generating the color-picker
	/*************************************************************************/

	ColorPicker.prototype.createPickingArea = function createPickingArea() {
		var area = document.createElement('div');
		var picker = document.createElement('div');

		area.className = 'picking-area';
		picker.className = 'picker';

		this.picking_area = area;
		this.color_picker = picker;
		setMouseTracking(area, this.updateColor.bind(this));

		area.appendChild(picker);
		this.node.appendChild(area);
	};

	ColorPicker.prototype.createHueArea = function createHueArea() {
		var area = document.createElement('div');
		var picker = document.createElement('div');

		area.className = 'hue';
		picker.className ='slider-picker';

		this.hue_area = area;
		this.hue_picker = picker;
		setMouseTracking(area, this.updateHueSlider.bind(this));

		area.appendChild(picker);
		this.node.appendChild(area);
	};

	ColorPicker.prototype.createAlphaArea = function createAlphaArea() {
		var mask = document.createElement('div');
		var picker = document.createElement('div');

		mask.className = 'alpha';
		picker.className = 'slider-picker';

		this.alpha_mask = mask;
		this.alpha_picker = picker;
		setMouseTracking(mask, this.updateAlphaSlider.bind(this));

		mask.appendChild(picker);
		this.node.appendChild(mask);
	};

	ColorPicker.prototype.createPreviewBox = function createPreviewBox(e) {
		var preview_box = document.createElement('div');
		var preview_color = document.createElement('div');

		preview_box.className = 'preview';
		preview_color.className = 'preview-color';

		this.preview_color = preview_color;

		preview_box.appendChild(preview_color);
		this.node.appendChild(preview_box);
	};

	ColorPicker.prototype.newInputComponent = function newInputComponent(title, topic, onChangeFunc) {
		var wrapper = document.createElement('div');
		var input = document.createElement('input');
		var info = document.createElement('span');

		wrapper.className = 'input';
		wrapper.setAttribute('data-topic', topic);
		info.textContent = title;
		info.className = 'name';
		input.setAttribute('type', 'text');

		wrapper.appendChild(info);
		wrapper.appendChild(input);
		this.node.appendChild(wrapper);

		input.addEventListener('change', onChangeFunc);
		input.addEventListener('click', function() {
			this.select();
		});

		this.subscribe(topic, function(value) {
			input.value = value;
		});
	};

	ColorPicker.prototype.createChangeModeButton = function createChangeModeButton() {

		var button = document.createElement('div');
		button.className = 'switch_mode';
		button.addEventListener('click', function() {
			if (this.picker_mode === 'HSV')
				this.setPickerMode('HSL');
			else
				this.setPickerMode('HSV');

		}.bind(this));

		this.node.appendChild(button);
	};

	/*************************************************************************/
	//					Updates properties of UI elements
	/*************************************************************************/

	ColorPicker.prototype.updateColor = function updateColor(e) {
		var x = e.pageX - this.picking_area.offsetLeft;
		var y = e.pageY - this.picking_area.offsetTop;

		// width and height should be the same
		var size = this.picking_area.clientWidth;

		if (x > size) x = size;
		if (y > size) y = size;
		if (x < 0) x = 0;
		if (y < 0) y = 0;

		var value = 100 - (y * 100 / size) | 0;
		var saturation = x * 100 / size | 0;

		if (this.picker_mode === 'HSV')
			this.color.setHSV(this.color.hue, saturation, value);
		if (this.picker_mode === 'HSL')
			this.color.setHSL(this.color.hue, saturation, value);

		this.color_picker.style.left = x + 'px';
		this.color_picker.style.top = y + 'px';

		this.updateAlphaGradient();
		this.updatePreviewColor();

		this.notify('value', value);
		this.notify('lightness', value);
		this.notify('saturation', saturation);

		this.notify('red', this.color.r);
		this.notify('green', this.color.g);
		this.notify('blue', this.color.b);
		this.notify('hexa', this.color.getHexa());

		notify(this.topic, this.color);
	};

	ColorPicker.prototype.updateHueSlider = function updateHueSlider(e) {
		var x = e.pageX - this.hue_area.offsetLeft;
		var width = this.hue_area.clientWidth;

		if (x < 0) x = 0;
		if (x > width) x = width;

		var hue = ((359 * x) / width) | 0;

		this.updateSliderPosition(this.hue_picker, x - 1);
		this.setHue(hue);
	};

	ColorPicker.prototype.updateAlphaSlider = function updateAlphaSlider(e) {
		var x = e.pageX - this.alpha_mask.offsetLeft;
		var width = this.alpha_mask.clientWidth;

		if (x < 0) x = 0;
		if (x > width) x = width;

		this.color.a = (x / width).toFixed(2);

		this.updateSliderPosition(this.alpha_picker, x - 1);
		this.updatePreviewColor();

		this.notify('alpha', this.color.a);
		notify(this.topic, this.color);
	};

	ColorPicker.prototype.setHue = function setHue(value) {
		this.color.setHue(value);

		this.updatePickerBackground();
		this.updateAlphaGradient();
		this.updatePreviewColor();

		this.notify('red', this.color.r);
		this.notify('green', this.color.g);
		this.notify('blue', this.color.b);
		this.notify('hexa', this.color.getHexa());
		this.notify('hue', this.color.hue);

		notify(this.topic, this.color);
	};

	// Updates when one of Saturation/Value/Lightness changes
	ColorPicker.prototype.updateSLV = function updateSLV() {
		this.updatePickerPosition();
		this.updateAlphaGradient();
		this.updatePreviewColor();

		this.notify('red', this.color.r);
		this.notify('green', this.color.g);
		this.notify('blue', this.color.b);
		this.notify('hexa', this.color.getHexa());

		notify(this.topic, this.color);
	};

	/*************************************************************************/
	//				Update positions of various UI elements
	/*************************************************************************/

	ColorPicker.prototype.updatePickerPosition = function updatePickerPosition() {
		var size = this.picking_area.clientWidth;
		var value = 0;

		if (this.picker_mode === 'HSV')
			value = this.color.value;
		if (this.picker_mode === 'HSL')
			value = this.color.lightness;

		var x = (this.color.saturation * size / 100) | 0;
		var y = size - (value * size / 100) | 0;

		this.color_picker.style.left = x + 'px';
		this.color_picker.style.top = y + 'px';
	};

	ColorPicker.prototype.updateSliderPosition = function updateSliderPosition(elem, pos) {
		elem.style.left = pos + 'px';
	};

	ColorPicker.prototype.updateHuePicker = function updateHuePicker() {
		var size = this.hue_area.clientWidth;
		var pos = (this.color.hue * size / 360 ) | 0;
		this.hue_picker.style.left = pos + 'px';
	};

	ColorPicker.prototype.updateAlphaPicker = function updateAlphaPicker() {
		var size = this.alpha_mask.clientWidth;
		var pos = (this.color.a * size) | 0;
		this.alpha_picker.style.left = pos + 'px';
	};

	/*************************************************************************/
	//						Update background colors
	/*************************************************************************/

	ColorPicker.prototype.updatePickerBackground = function updatePickerBackground() {
		var nc = new Color(this.color);
		nc.setHSV(nc.hue, 100, 100);
		this.picking_area.style.backgroundColor = nc.getHexa();
	};

	ColorPicker.prototype.updateAlphaGradient = function updateAlphaGradient() {
		this.alpha_mask.style.backgroundColor = this.color.getHexa();
	};

	ColorPicker.prototype.updatePreviewColor = function updatePreviewColor() {
		this.preview_color.style.backgroundColor = this.color.getColor();
	};

	/*************************************************************************/
	//						Update input elements
	/*************************************************************************/

	ColorPicker.prototype.inputChangeHue = function inputChangeHue(e) {
		var value = parseInt(e.target.value);
		this.setHue(value);
		this.updateHuePicker();
	};

	ColorPicker.prototype.inputChangeSaturation = function inputChangeSaturation(e) {
		var value = parseInt(e.target.value);
		this.color.setSaturation(value);
		e.target.value = this.color.saturation;
		this.updateSLV();
	};

	ColorPicker.prototype.inputChangeValue = function inputChangeValue(e) {
		var value = parseInt(e.target.value);
		this.color.setValue(value);
		e.target.value = this.color.value;
		this.updateSLV();
	};

	ColorPicker.prototype.inputChangeLightness = function inputChangeLightness(e) {
		var value = parseInt(e.target.value);
		this.color.setLightness(value);
		e.target.value = this.color.lightness;
		this.updateSLV();
	};

	ColorPicker.prototype.inputChangeRed = function inputChangeRed(e) {
		var value = parseInt(e.target.value);
		this.color.setByName('r', value);
		e.target.value = this.color.r;
		this.setColor(this.color);
	};

	ColorPicker.prototype.inputChangeGreen = function inputChangeGreen(e) {
		var value = parseInt(e.target.value);
		this.color.setByName('g', value);
		e.target.value = this.color.g;
		this.setColor(this.color);
	};

	ColorPicker.prototype.inputChangeBlue = function inputChangeBlue(e) {
		var value = parseInt(e.target.value);
		this.color.setByName('b', value);
		e.target.value = this.color.b;
		this.setColor(this.color);
	};

	ColorPicker.prototype.inputChangeAlpha = function inputChangeAlpha(e) {
		var value = parseFloat(e.target.value);

		if (typeof value === 'number' && isNaN(value) === false &&
			value >= 0 && value <= 1)
			this.color.a = value.toFixed(2);

		e.target.value = this.color.a;
		this.updateAlphaPicker();
		this.updatePreviewColor();
	};

	ColorPicker.prototype.inputChangeHexa = function inputChangeHexa(e) {
		var value = e.target.value;
		this.color.setHexa(value);
		this.setColor(this.color);
	};

	/*************************************************************************/
	//							Internal Pub/Sub
	/*************************************************************************/

	ColorPicker.prototype.subscribe = function subscribe(topic, callback) {
		this.subscribers[topic] = callback;
	};

	ColorPicker.prototype.notify = function notify(topic, value) {
		if (this.subscribers[topic])
			this.subscribers[topic](value);
	};

	/*************************************************************************/
	//							Set Picker Properties
	/*************************************************************************/

	ColorPicker.prototype.setColor = function setColor(color) {
		if(color instanceof Color !== true) {
			console.log('Typeof parameter not Color');
			return;
		}

		if (color.format !== this.picker_mode) {
			color.setFormat(this.picker_mode);
			color.updateHSX();
		}

		this.color.copy(color);
		this.updateHuePicker();
		this.updatePickerPosition();
		this.updatePickerBackground();
		this.updateAlphaPicker();
		this.updatePreviewColor();
		this.updateAlphaGradient();

		this.notify('red', this.color.r);
		this.notify('green', this.color.g);
		this.notify('blue', this.color.b);

		this.notify('hue', this.color.hue);
		this.notify('saturation', this.color.saturation);
		this.notify('value', this.color.value);
		this.notify('lightness', this.color.lightness);

		this.notify('alpha', this.color.a);
		this.notify('hexa', this.color.getHexa());
		notify(this.topic, this.color);
	};

	ColorPicker.prototype.setPickerMode = function setPickerMode(mode) {
		if (mode !== 'HSV' && mode !== 'HSL')
			return;

		this.picker_mode = mode;
		this.node.setAttribute('data-mode', this.picker_mode);
		this.setColor(this.color);
	};

	/*************************************************************************/
	//								UNUSED
	/*************************************************************************/

	var setPickerMode = function setPickerMode(topic, mode) {
		if (pickers[topic])
			pickers[topic].setPickerMode(mode);
	};

	var setColor = function setColor(topic, color) {
		if (pickers[topic])
			pickers[topic].setColor(color);
	};

	var getColor = function getColor(topic) {
		if (pickers[topic])
			return new Color(pickers[topic].color);
	};

	var subscribe = function subscribe(topic, callback) {
		if (subscribers[topic] === undefined)
			subscribers[topic] = [];

		subscribers[topic].push(callback);
	};

	var unsubscribe = function unsubscribe(callback) {
		subscribers.indexOf(callback);
		subscribers.splice(index, 1);
	};

	var notify = function notify(topic, value) {
		if (subscribers[topic] === undefined || subscribers[topic].length === 0)
			return;

		var color = new Color(value);
		for (var i in subscribers[topic])
			subscribers[topic][i](color);
	};

	var init = function init() {
		var elem = document.querySelectorAll('.ui-color-picker');
		var size = elem.length;
		for (var i = 0; i < size; i++)
			new ColorPicker(elem[i]);
	};

	return {
		init : init,
		Color : Color,
		RGBColor : RGBColor,
		RGBAColor : RGBAColor,
		HSVColor : HSVColor,
		HSVAColor : HSVAColor,
		HSLColor : HSLColor,
		HSLAColor : HSLAColor,
		setColor : setColor,
		getColor : getColor,
		subscribe : subscribe,
		unsubscribe : unsubscribe,
		setPickerMode : setPickerMode
	};

})();

/**
 * UI-DropDown Select
 */

var DropDownManager = (function DropdownManager() {
	'use strict';

	var subscribers = {};
	var dropdowns = [];
	var active = null;

	var visbility = ["hidden", "visible"];

	var DropDown = function DropDown(node) {
		var topic = node.getAttribute('data-topic');
		var label = node.getAttribute('data-label');
		var selected = node.getAttribute('data-selected') | 0;

		var select = document.createElement('div');
		var list = document.createElement('div');
		var uval = 0;
		var option = null;
		var option_value = null;

		list.className = 'ui-dropdown-list';
		select.className = 'ui-dropdown-select';

		while (node.firstElementChild !== null) {
			option = node.firstElementChild;
			option_value = option.getAttribute('data-value');

			if (option_value === null)
				option.setAttribute('data-value', uval);

			list.appendChild(node.firstElementChild);
			uval++;
		}

		node.appendChild(select);
		node.appendChild(list);

		select.onclick = this.toggle.bind(this);
		list.onclick = this.updateValue.bind(this);
		document.addEventListener('click', clickOut);

		this.state = 0;
		this.time = 0;
		this.dropmenu = list;
		this.select = select;
		this.toggle(false);
		this.value = {};
		this.topic = topic;

		if (label)
			select.textContent = label;
		else
			this.setNodeValue(list.children[selected]);

		dropdowns[topic] = this;

	};

	DropDown.prototype.toggle = function toggle(state) {
		if (typeof(state) === 'boolean')
			this.state = state === false ? 0 : 1;
		else
			this.state = 1 ^ this.state;

		if (active !== this) {
			if (active)
				active.toggle(false);
			active = this;
		}

		if (this.state === 0)
			this.dropmenu.setAttribute('data-hidden', 'true');
		else
			this.dropmenu.removeAttribute('data-hidden');

	};

	DropDown.prototype.updateValue = function updateValue(e) {

		if (Date.now() - this.time < 500)
			return;

		if (e.target.className !== "ui-dropdown-list") {
			this.setNodeValue(e.target);
			this.toggle(false);
		}

		this.time = Date.now();
	};

	DropDown.prototype.setNodeValue = function setNodeValue(node, send_notify) {
		this.value['name'] = node.textContent;
		this.value['value'] = node.getAttribute('data-value');

		this.select.textContent = node.textContent;
		this.select.setAttribute('data-value', this.value['value']);

		if (send_notify !== false)
			notify.call(this);
	};

	var clickOut = function clickOut(e) {
		if (active.state === 0 ||
			e.target === active.dropmenu ||
			e.target === active.select)
			return;

		active.toggle(false);
	};

	var createDropDown = function createDropDown(topic, options) {

		var dropdown = document.createElement('div');
		dropdown.setAttribute('data-topic', topic);
		dropdown.className = 'ui-dropdown';

		for (var i in options) {
			var x = document.createElement('div');
			x.setAttribute('data-value', i);
			x.textContent = options[i];
			dropdown.appendChild(x);
		}

		new DropDown(dropdown);

		return dropdown;
	};

	var setValue = function setValue(topic, index, send_notify) {
		if (dropdowns[topic] === undefined ||
			index >= dropdowns[topic].dropmenu.children.length)
			return;

		dropdowns[topic].setNodeValue(dropdowns[topic].dropmenu.children[index], send_notify);
	};

	var subscribe = function subscribe(topic, callback) {
		if (subscribers[topic] === undefined)
			subscribers[topic] = [];
		subscribers[topic].push(callback);
	};

	var unsubscribe = function unsubscribe(topic, callback) {
		var index = subscribers[topic].indexOf(callback);
		subscribers[topic].splice(index, 1);
	};

	var notify = function notify() {
		if (subscribers[this.topic] === undefined)
			return;

		for (var i in subscribers[this.topic]) {
			subscribers[this.topic][i](this.value);
		}
	};

	var init = function init() {
		var elem, size;

		elem = document.querySelectorAll('.ui-dropdown');
		size = elem.length;
		for (var i = 0; i < size; i++)
			new DropDown(elem[i]);

	};

	return {
		init : init,
		setValue : setValue,
		subscribe : subscribe,
		unsubscribe : unsubscribe,
		createDropDown : createDropDown
	};

})();

/**
 * UI-SlidersManager
 */

var InputSliderManager = (function InputSliderManager() {
	'use strict';

	var subscribers = {};
	var sliders = [];

	var InputComponent = function InputComponent(obj) {
		var input = document.createElement('input');
		input.setAttribute('type', 'text');
		input.style.width = 50 + obj.precision * 10 + 'px';

		input.addEventListener('click', function(e) {
			this.select();
		});

		input.addEventListener('change', function(e) {
			var value = parseFloat(e.target.value);

			if (isNaN(value) === true)
				setValue(obj.topic, obj.value);
			else
				setValue(obj.topic, value);
		});

		return input;
	};

	var SliderComponent = function SliderComponent(obj, sign) {
		var slider = document.createElement('div');
		var startX = null;
		var start_value = 0;

		slider.addEventListener("click", function(e) {
			document.removeEventListener("mousemove", sliderMotion);
			setValue(obj.topic, obj.value + obj.step * sign);
		});

		slider.addEventListener("mousedown", function(e) {
			startX = e.clientX;
			start_value = obj.value;
			document.body.style.cursor = "e-resize";

			document.addEventListener("mouseup", slideEnd);
			document.addEventListener("mousemove", sliderMotion);
		});

		var slideEnd = function slideEnd(e) {
			document.removeEventListener("mousemove", sliderMotion);
			document.body.style.cursor = "auto";
			slider.style.cursor = "pointer";
		};

		var sliderMotion = function sliderMotion(e) {
			slider.style.cursor = "e-resize";
			var delta = (e.clientX - startX) / obj.sensivity | 0;
			var value = delta * obj.step + start_value;
			setValue(obj.topic, value);
		};

		return slider;
	};

	var InputSlider = function(node) {
		var min		= parseFloat(node.getAttribute('data-min'));
		var max		= parseFloat(node.getAttribute('data-max'));
		var step	= parseFloat(node.getAttribute('data-step'));
		var value	= parseFloat(node.getAttribute('data-value'));
		var topic	= node.getAttribute('data-topic');
		var unit	= node.getAttribute('data-unit');
		var name 	= node.getAttribute('data-info');
		var sensivity = node.getAttribute('data-sensivity') | 0;
		var precision = node.getAttribute('data-precision') | 0;

		this.min = isNaN(min) ? 0 : min;
		this.max = isNaN(max) ? 100 : max;
		this.precision = precision >= 0 ? precision : 0;
		this.step = step < 0 || isNaN(step) ? 1 : step.toFixed(precision);
		this.topic = topic;
		this.node = node;
		this.unit = unit === null ? '' : unit;
		this.sensivity = sensivity > 0 ? sensivity : 5;
		value = isNaN(value) ? this.min : value;

		var input = new InputComponent(this);
		var slider_left  = new SliderComponent(this, -1);
		var slider_right = new SliderComponent(this,  1);

		slider_left.className = 'ui-input-slider-left';
		slider_right.className = 'ui-input-slider-right';

		if (name) {
			var info = document.createElement('span');
			info.className = 'ui-input-slider-info';
			info.textContent = name;
			node.appendChild(info);
		}

		node.appendChild(slider_left);
		node.appendChild(input);
		node.appendChild(slider_right);

		this.input = input;
		sliders[topic] = this;
		setValue(topic, value);
	};

	InputSlider.prototype.setInputValue = function setInputValue() {
		this.input.value = this.value.toFixed(this.precision) + this.unit;
	};

	var setValue = function setValue(topic, value, send_notify) {
		var slider = sliders[topic];
		if (slider === undefined)
			return;

		value = parseFloat(value.toFixed(slider.precision));

		if (value > slider.max) value = slider.max;
		if (value < slider.min)	value = slider.min;

		slider.value = value;
		slider.node.setAttribute('data-value', value);

		slider.setInputValue();

		if (send_notify === false)
			return;

		notify.call(slider);
	};

	var setMax = function setMax(topic, value) {
		var slider = sliders[topic];
		if (slider === undefined)
			return;

		slider.max = value;
		setValue(topic, slider.value);
	};

	var setMin = function setMin(topic, value) {
		var slider = sliders[topic];
		if (slider === undefined)
			return;

		slider.min = value;
		setValue(topic, slider.value);
	};

	var setUnit = function setUnit(topic, unit, send_notify) {
		var slider = sliders[topic];
		if (slider === undefined)
			return;

		slider.unit = unit;
		setValue(topic, slider.value, send_notify);
	};

	var setStep = function setStep(topic, value) {
		var slider = sliders[topic];
		if (slider === undefined)
			return;

		slider.step = parseFloat(value);
		setValue(topic, slider.value);
	};

	var setPrecision = function setPrecision(topic, value) {
		var slider = sliders[topic];
		if (slider === undefined)
			return;

		value = value | 0;
		slider.precision = value;

		var step = parseFloat(slider.step.toFixed(value));
		if (step === 0)
			slider.step = 1 / Math.pow(10, value);

		setValue(topic, slider.value);
	};

	var setSensivity = function setSensivity(topic, value) {
		var slider = sliders[topic];
		if (slider === undefined)
			return;

		value = value | 0;

		slider.sensivity = value > 0 ? value : 5;
	};

	var getNode =  function getNode(topic) {
		return sliders[topic].node;
	};

	var getPrecision =  function getPrecision(topic) {
		return sliders[topic].precision;
	};

	var getStep =  function getStep(topic) {
		return sliders[topic].step;
	};

	var subscribe = function subscribe(topic, callback) {
		if (subscribers[topic] === undefined)
			subscribers[topic] = [];
		subscribers[topic].push(callback);
	};

	var unsubscribe = function unsubscribe(topic, callback) {
		subscribers[topic].indexOf(callback);
		subscribers[topic].splice(index, 1);
	};

	var notify = function notify() {
		if (subscribers[this.topic] === undefined)
			return;
		for (var i = 0; i < subscribers[this.topic].length; i++)
			subscribers[this.topic][i](this.value);
	};

	var createSlider = function createSlider(topic, label) {
		var slider = document.createElement('div');
		slider.className = 'ui-input-slider';
		slider.setAttribute('data-topic', topic);

		if (label !== undefined)
			slider.setAttribute('data-info', label);

		new InputSlider(slider);
		return slider;
	};

	var init = function init() {
		var elem = document.querySelectorAll('.ui-input-slider');
		var size = elem.length;
		for (var i = 0; i < size; i++)
			new InputSlider(elem[i]);
	};

	return {
		init : init,
		setMax : setMax,
		setMin : setMin,
		setUnit : setUnit,
		setStep : setStep,
		getNode : getNode,
		getStep : getStep,
		setValue : setValue,
		subscribe : subscribe,
		unsubscribe : unsubscribe,
		setPrecision : setPrecision,
		setSensivity : setSensivity,
		getPrecision : getPrecision,
		createSlider : createSlider,
	};

})();

/**
 * UI-SlidersManager
 */

var SliderManager = (function SliderManager() {
	'use strict';

	var subscribers = {};
	var sliders = [];

	function trackMouse(elem, callback, startFunc, endFunc) {
		startFunc = startFunc || function(e) {};
		endFunc = endFunc || function(e) {};

		elem.addEventListener('mousedown', function(e) {
			e.preventDefault();
			startFunc(e);

			document.addEventListener('mousemove', callback);
			document.addEventListener('mouseup', function up(e) {
				document.removeEventListener('mousemove', callback);
				document.removeEventListener('mouseup', up);
				endFunc(e);
			});
		});

		elem.addEventListener('click', function(e) {
			e.stopPropagation();
		});
	}

	var Slider = function(node) {
		var topic = node.getAttribute('data-topic');
		var info = node.getAttribute('data-info');
		var unit = node.getAttribute('data-unit');
		var min = node.getAttribute('data-min') | 0;
		var max = node.getAttribute('data-max') | 0;
		var step = node.getAttribute('data-step') | 0;
		var value = node.getAttribute('data-value') | 0;
		var snap = node.getAttribute('data-snap');

		this.min = min;
		this.max = max > 0 ? max : 100;
		this.step = step === 0 ? 1 : step;
		this.value = value <= max && value >= min ? value : (min + max) / 2 | 0;
		this.snap = snap === "true" ? true : false;
		this.topic = topic;
		this.unit = unit;

		var name = document.createElement('div');
		var slider = document.createElement('div');
		var pointer = document.createElement('div');

		name.className = 'ui-slider-name';
		name.textContent = info;

		slider.className = 'ui-slider-slider';
		pointer.className = 'ui-slider-pointer';

		node.appendChild(name);
		slider.appendChild(pointer);

		this.pointer = pointer;
		this.slider = slider;
		this.node = node;

		this.createSetButton('-', this.decrement.bind(this));
		node.appendChild(slider);
		this.createSetButton('+', this.increment.bind(this));
		this.createInputField();

		new trackMouse(slider, this.updateSlider.bind(this), this.startMove.bind(this));
		slider.addEventListener('click', function(e) {
			this.startMove();
			this.updateSlider(e);
		}.bind(this));

		sliders[topic] = this;
		this.setValue(this.value);
	};

	Slider.prototype.createSetButton = function createSetButton(type, callback) {
		var button = document.createElement('div');
		button.className = 'ui-slider-button';
		button.textContent = type;
		button.addEventListener("click", callback);
		this.node.appendChild(button);
	};

	Slider.prototype.createInputField = function createInputField() {
		var input = document.createElement('input');
		input.setAttribute('type', 'text');
		this.node.appendChild(input);

		input.addEventListener('click', function(e) {
			this.select();
		});

		input.addEventListener('change', function(e) {
			this.setValue(e.target.value | 0);
		}.bind(this));

		subscribe(this.topic, function(value) {
			input.value = value + this.unit;
		}.bind(this));
	};

	Slider.prototype.startMove = function startMove(e) {
		this.sliderX = this.slider.getBoundingClientRect().left;
	};

	Slider.prototype.updateSlider = function updateSlider(e) {
		var width = this.slider.clientWidth;
		var pos = e.clientX - this.sliderX;
		var delta = this.max - this.min;

		if (pos < 0) pos = 0;
		if (pos > width) pos = width;

		var value = pos * delta / width | 0;
		var precision = value % this.step;
		value = value - precision + this.min;

		if (precision > this.step / 2)
			value = value + this.step;

		if (this.snap)
			pos =  (value - this.min) * width / delta;

		this.pointer.style.left = pos + "px";
		this.value = value;
		notify.call(this);
	};

	Slider.prototype.increment = function increment() {
		this.setValue(this.value + this.step);
	};

	Slider.prototype.decrement = function decrement() {
		this.setValue(this.value - this.step);
	};

	Slider.prototype.setValue = function setValue(value) {
		if (value > this.max || value < this.min)
			return;

		var delta = this.max - this.min;
		var width = this.slider.clientWidth;
		var pos =  (value - this.min) * width / delta | 0;
		this.value = value;
		this.pointer.style.left = pos + "px";
		notify.call(this);
	};

	var setValue = function setValue(topic, value) {
		var slider = sliders[topic];
		slider.setValue(value);
	};

	var subscribe = function subscribe(topic, callback) {
		if (subscribers[topic] === undefined)
			subscribers[topic] = [];
		subscribers[topic].push(callback);
	};

	var unsubscribe = function unsubscribe(topic, callback) {
		subscribers[topic].indexOf(callback);
		subscribers[topic].splice(index, 1);
	};

	var notify = function notify() {
		if (subscribers[this.topic] === undefined)
			return;
		for (var i = 0; i < subscribers[this.topic].length; i++)
			subscribers[this.topic][i](this.value);
	};

	var init = function init() {
		var elem = document.querySelectorAll('.ui-slider');
		var size = elem.length;
		for (var i = 0; i < size; i++)
			new Slider(elem[i]);
	};

	return {
		init : init,
		setValue : setValue,
		subscribe : subscribe,
		unsubscribe : unsubscribe
	};

})();

window.addEventListener("load", function() {
	LinearGradientTool.init();
});

window.addEventListener("load", function() {
	LinearGradientTool.init();
});

var LinearGradientTool = (function LinearGradientTool() {
	'use strict';

	var radian = 180 / Math.PI;
	var inv_radian = Math.PI / 180;
	var units = {'%': 1, 'px' : 0};

	/*========== DOM Methods ==========*/

	function getElemById(id) {
		return document.getElementById(id);
	}

	function allowDropEvent(e) {
		e.preventDefault();
	}

	function createClassElement(tag, className, parent) {
		var elem = document.createElement(tag);
		elem.className = className;
		if (parent) parent.appendChild(elem);
		return elem;
	};

	function trackMouse(elem, callback, startFunc, endFunc) {
		startFunc = startFunc || function(e) {};
		endFunc = endFunc || function(e) {};

		elem.addEventListener('mousedown', function(e) {
			e.preventDefault();
			startFunc(e);

			document.addEventListener('mousemove', callback);

			document.addEventListener('mouseup', function up(e) {
				document.removeEventListener('mousemove', callback);
				document.removeEventListener('mouseup', up);
				endFunc(e);
			});
		});

		elem.addEventListener('click', function(e) {
			e.stopPropagation();
		});
	}

	var Color = UIColorPicker.Color;
	var HSVColor = UIColorPicker.HSVColor;

	var UIComponent = (function UIComponent() {

		function makeResizable(elem, axis, callback, endFunc) {
			var valueX = 0;
			var valueY = 0;
			var action = 0;
			var callback = typeof callback === "function" ? callback : null;

			endFunc = endFunc || function(e) {};

			var resizeStart = function resizeStart(e) {
				e.stopPropagation();
				e.preventDefault();
				if (e.button !== 0)
					return;

				valueX = e.clientX - elem.clientWidth;
				valueY = e.clientY - elem.clientHeight;

				document.body.setAttribute('data-resize', axis);
				document.addEventListener('mousemove', mouseMove);
				document.addEventListener('mouseup', resizeEnd);
			};

			var mouseMove = function mouseMove(e) {
				if (action >= 0)
					elem.style.width = e.clientX - valueX + 'px';
				if (action <= 0)
					elem.style.height = e.clientY - valueY + 'px';
				if (callback)
					callback();
			};

			var resizeEnd = function resizeEnd(e) {
				if (e.button !== 0)
					return;

				document.body.removeAttribute('data-resize', axis);
				document.removeEventListener('mousemove', mouseMove);
				document.removeEventListener('mouseup', resizeEnd);
				endFunc();
			};

			var handle = document.createElement('div');
			handle.className = 'resize-handle';

			if (axis === 'width') action = 1;
			else if (axis === 'height') action = -1;
			else axis = 'both';

			handle.className = 'resize-handle';
			handle.setAttribute('data-resize', axis);
			handle.addEventListener('mousedown', resizeStart);
			elem.appendChild(handle);
		};

		return {
			makeResizable : makeResizable
		};
	})();


	/**
	 * Gradient Point
	 */
	var GradientPoint = function GradientPoint(Axis) {
		var point = document.createElement('div');

		point.className = 'gradient-point';

		this.position = 0;
		this.node = point;
		this.Axis = Axis;
		this.color = new HSVColor(0, 0, 100);
		this.CSScolor = this.color.getColor();
		this.CSSposition = 0;
		this.PrevPoint = null;
		this.NextPoint = null;

		this.Axis.num_points++;

		point.addEventListener('click', this.activate.bind(this));
		trackMouse(point, this.updatePositionM.bind(this), this.startMove.bind(this),
			this.endMove.bind(this));

		Axis.line.appendChild(point);
		return this;
	};

	GradientPoint.prototype.deletePoint = function deletePoint() {
		this.Axis.line.removeChild(this.node);
	};

	GradientPoint.prototype.activate = function activate() {
		if (this.Axis.state === false)
			return;

		this.Axis.setActivePoint(this);
		this.node.setAttribute('data-active', 'true');
		UIColorPicker.setColor('picker', this.color);
		InputSliderManager.setValue('point-position', this.CSSposition);
		if (this.Axis.num_points > 2)
			AxesManager.setDeleteButtonState('active');
	};

	GradientPoint.prototype.deactivate = function deactivate() {
		this.node.removeAttribute('data-active');
	};

	GradientPoint.prototype.startMove = function startMove(e) {
		this.Axis.updateCenterPointPos();
		this.node.setAttribute('data-active', 'true');
		document.body.setAttribute('data-dragging', 'true');
	};

	GradientPoint.prototype.endMove = function endMove(e) {
		this.node.removeAttribute('data-active', 'true');
		document.body.removeAttribute('data-dragging');
	};

	GradientPoint.prototype.updatePositionM = function updatePositionM(e) {
		var A = this.Axis;
		var Qx = e.clientX - A.centerX;
		var Qy = e.clientY - A.centerY;
		this.position = (A.Px * Qx + A.Py * Qy) / A.Pmod + A.lsize;
		this.updateCSSPosition();
		this.Axis.reorderPoint(this);
		this.Axis.updateGradient();
		this.updateSlider();
	};

	GradientPoint.prototype.setPositionM = function setPositionM(posX, posY) {
		var A = this.Axis;
		var Qx = posX - A.centerX;
		var Qy = posY - A.centerY;
		this.position = (A.Px * Qx + A.Py * Qy) / A.Pmod + A.lsize;
		this.updateCSSPosition();
		this.Axis.reorderPoint(this);
		this.Axis.updateGradient();
		this.updateSlider();
	};

	GradientPoint.prototype.updateAbsolutePosition = function updateAbsolutePosition() {
		if (this.Axis.unit ='%')
			this.position = parseFloat(((this.CSSposition / 100) * (2 * this.Axis.lsize)).toFixed(1));;
	};

	GradientPoint.prototype.setPosition = function setPosition(pos) {
		this.position = pos;
		if (this.Axis.unit === '%')
			this.position = parseFloat(((this.position / 100) * (2 * this.Axis.lsize)).toFixed(1));;
		this.updateCSSPosition();
		this.Axis.reorderPoint(this);
		this.Axis.updateGradient();
	};

	GradientPoint.prototype.updateSlider = function updateSlider() {
		if (this.Axis.ActivePoint === this && this.Axis.state === true)
			InputSliderManager.setValue('point-position', this.CSSposition, false);
	};

	GradientPoint.prototype.updateColor = function updateColor(color) {
		this.color.copy(color);
		this.CSScolor = color.getColor();
		this.updateCSSvalue();
	};

	GradientPoint.prototype.updateCSSPosition = function updateCSSPosition() {
		this.CSSposition = this.position | 0;
		if (this.Axis.unit === '%')
			this.CSSposition = parseFloat((100 * this.position / (2 * this.Axis.lsize)).toFixed(1));

		this.node.style.left = this.CSSposition + this.Axis.unit;
		this.updateCSSvalue();
	};

	GradientPoint.prototype.updateCSSvalue = function updateCSSvalue() {
		this.CSSvalue = this.CSScolor + ' ' + this.CSSposition + this.Axis.unit;
	};

	GradientPoint.prototype.insertBefore = function insertBefore(point) {
		this.NextPoint = point;
		this.PrevPoint = point.PrevPoint;
		point.PrevPoint = this;
		if (this.PrevPoint)
			this.PrevPoint.NextPoint = this;
	};

	GradientPoint.prototype.insertAfter = function insertAfter(point) {
		this.NextPoint = point.NextPoint;
		this.PrevPoint = point;
		point.NextPoint = this;
		if (this.NextPoint)
			this.NextPoint.PrevPoint = this;
	};


	/**
	 * Gradient Axis
	 */
	function GradientAxis(container, id) {
		var axis = createClassElement('div', 'gradient-axis', null);
		var line = createClassElement('div', 'gradient-line', axis);
		var rotate_point = createClassElement('div', 'rotate-point', axis);

		axis.setAttribute('axisID', id);

		var svg = this.createSVGArrow(id);
		rotate_point.appendChild(svg);

		this.id 	= id;
		this.axis	= axis;
		this.unit	= '%';
		this.line	= line;
		this.container = container;
		this.lsize = container.clientWidth / 2;
		this.FirstPoint = null;
		this.ActivePoint = null;
		this.gradient = '';
		this.num_points = 0;

		this.size = 0;
		this.angle = 0;
		this.state = false;
		this.updateOnResize();

		trackMouse(rotate_point, this.updateAxisAngle.bind(this),
						this.startRotation.bind(this));

		container.appendChild(axis);
		line.addEventListener('click', this.placeGradientPoint.bind(this));
	};


	GradientAxis.prototype.createSVGArrow = function createSVGArrow(id) {
		var xmlns = 'http://www.w3.org/2000/svg';
		var svg = document.createElementNS(xmlns, 'svg');
		var path = document.createElementNS(xmlns, 'path');

		svg.setAttribute('class', 'gradient-arrow');

		svg.setAttribute('width', '25');
		svg.setAttribute('height', '25');

		path.setAttribute('fill', '#CCC');
		path.setAttribute('d', 'M 25,12.5 L 0,0 L 7.5,12.5 L 0,25');
		svg.appendChild(path);

		return svg;
	};

	GradientAxis.prototype.placeGradientPoint = function placeGradientPoint(e) {
		this.updateCenterPointPos();
		var point = new GradientPoint(this);
		point.setPositionM(e.clientX, e.clientY);
		point.activate();
		this.attachPoint(point);
		this.updateGradient();
	};

	GradientAxis.prototype.newGradientPoint = function newGradientPoint(pos) {
		var point = new GradientPoint(this);
		point.setPosition(pos);
		point.activate();
		this.attachPoint(point);
		this.updateGradient();
	};

	GradientAxis.prototype.attachPoint = function attachPoint(point) {

		// add the first point
		if (this.FirstPoint === null) {
			this.FirstPoint = point;
			return;
		}

		// insert the point into the list
		var p = this.FirstPoint;
		while (p.NextPoint) {
			if (point.CSSposition < p.CSSposition) {
				point.insertBefore(p);
				if (point.PrevPoint === null)
					this.FirstPoint = point;
				return;
			}
			p = p.NextPoint;
		};

		// test the last point
		if (point.CSSposition < p.CSSposition)
			point.insertBefore(p);
		else
			point.insertAfter(p);

		if (point.PrevPoint === null)
			this.FirstPoint = point;

		return;
	};

	GradientAxis.prototype.detachPoint = function detachPoint(point) {
		if (this.FirstPoint === point)
			this.FirstPoint = point.NextPoint;
		if (point.PrevPoint)
			point.PrevPoint.NextPoint = point.NextPoint;
		if (point.NextPoint)
			point.NextPoint.PrevPoint = point.PrevPoint;
		point.NextPoint = null;
		point.PrevPoint = null;
	};

	GradientAxis.prototype.deleteActivePoint = function deleteActivePoint() {
		// return if only 2 points left on the axis
		if (this.num_points === 2)
			return;

		if (this.ActivePoint) {
			this.ActivePoint.deletePoint();
			this.detachPoint(this.ActivePoint);
			this.updateGradient();
			this.num_points--;
		}
	};

	GradientAxis.prototype.reorderPoint = function reorderPoint(point) {
		if (point.NextPoint && point.NextPoint.CSSposition > point.CSSposition &&
			point.PrevPoint && point.PrevPoint.CSSposition < point.CSSposition)
			return;
		if (point.NextPoint === point.PrevPoint)
			return;

		this.detachPoint(point);
		this.attachPoint(point);
	};

	GradientAxis.prototype.setActivePoint = function setActivePoint(point) {
		if (this.ActivePoint)
			this.ActivePoint.deactivate();
		this.ActivePoint = point;
	};

	GradientAxis.prototype.activate = function activate() {
		this.state = true;
		this.axis.setAttribute('data-active', this.state);
		InputSliderManager.setUnit('point-position', this.unit, false);
		SliderManager.setValue('axis-rotation', this.angle | 0, false);
		DropDownManager.setValue('axis-unit', units[this.unit], false);

		if (this.ActivePoint)
			this.ActivePoint.activate();
	};

	GradientAxis.prototype.deactivate = function deactivate() {
		this.state = false;
		this.axis.removeAttribute('data-active', this.state);
		if (this.ActivePoint)
			this.ActivePoint.deactivate();
	};

	GradientAxis.prototype.deleteAxis = function deleteAxis() {
		this.deactivate();
		this.container.removeChild(this.axis);
	};

	GradientAxis.prototype.updatePointColor = function updatePointColor(color) {
		if (this.ActivePoint)
			this.ActivePoint.updateColor(color);
		this.updateGradient();
	};

	GradientAxis.prototype.updatePointPosition = function updatePointPosition(value) {
		if (this.ActivePoint)
			this.ActivePoint.setPosition(value);
	};

	GradientAxis.prototype.setUnit = function setUnit(unit) {
		this.unit = unit;
		this.updateAllPoints();
		InputSliderManager.setUnit('point-position', unit, false);

		if (this.ActivePoint)
			this.ActivePoint.updateSlider();

		this.updateGradient();
	};

	GradientAxis.prototype.updateAllPoints = function updateAllPoints() {
		var p = this.FirstPoint;
		while(p) {
			p.updateCSSPosition();
			p = p.NextPoint;
		}
	};

	/* Axis events */
	GradientAxis.prototype.startRotation = function startRotation(e) {
		this.updateCenterPointPos();
		this.updateAxisAngle(e);
	};

	GradientAxis.prototype.updateOnResize = function updateOnResize() {
		this.updateContainer();
		this.updateCenterPointPos();
		this.setAxisAngle(this.angle);
	};

	GradientAxis.prototype.updateCenterPointPos = function updateCenterPointPos() {
		var pos = this.container.getBoundingClientRect();
		this.centerX = (pos.left + pos.right) / 2;
		this.centerY = (pos.top + pos.bottom) / 2;
	};

	GradientAxis.prototype.updateContainer = function updateContainer() {
		var W = this.container.clientWidth;
		var H = this.container.clientHeight;

		var max_size = Math.sqrt(W * W + H * H) + 50;

		this.axis.style.width = max_size + 'px';
		this.axis.style.left = (W - max_size)/2 - 1 + 'px';

		this.mW = W / 2;
		this.mH = H / 2;
	};

	GradientAxis.prototype.updateAxisAngle = function updateAxisAngle(e) {

		var Px = e.clientX - this.centerX;
		var Py = e.clientY - this.centerY;
		var deg = -Math.atan2(Py, Px) * radian;
		var Pmod = Math.sqrt(Px * Px + Py * Py);
		this.lsize = (this.mW * Math.abs(Px) + this.mH * Math.abs(Py)) / Pmod;

		if (this.state === true)
			SliderManager.setValue('axis-rotation', deg | 0, false);

		this.angle = deg;
		this.updateCSS();
		AxesManager.updateCSSGradient();

		this.Px = Px;
		this.Py = Py;
		this.Pmod = Pmod;
	};

	GradientAxis.prototype.setAxisAngle = function setAxisAngle(deg) {
		var rad = -deg * inv_radian;
		var Px = Math.cos(rad);
		var Py = Math.sin(rad);
		this.lsize = this.mW * Math.abs(Px) + this.mH * Math.abs(Py);

		this.angle = deg;
		this.updateCSS();
		AxesManager.updateCSSGradient();

		this.Px = Px;
		this.Py = Py;
		this.Pmod = 1;
	};

	/* UI Methods - apply CSS */

	GradientAxis.prototype.updateCSS = function updateCSS() {
		this.line.style.width = 2 * this.lsize + 'px';
		this.axis.style.transform = 'rotate('+ -this.angle +'deg)';
		this.axis.style.webkitTransform = 'rotate('+ -this.angle +'deg)';
	};

	GradientAxis.prototype.updateGradient = function updateGradient() {
		var p = this.FirstPoint;
		if (p === null)
			return;

		this.gradient = p.CSSvalue;
		p = p.NextPoint;
		while(p) {
			this.gradient += ', ' + p.CSSvalue;
			p = p.NextPoint;
		};
		AxesManager.updateCSSGradient();
	};

	// this is the standard syntax
	GradientAxis.prototype.getCSSGradient = function getCSSGradient() {
		return 'linear-gradient('+ (-this.angle + 90 | 0) +'deg, ' + this.gradient + ')';
	};

	/**
	 * AxesManager
	 */
	var AxesManager = (function AxesManager() {

		var lg_axes = [];
		var ActiveAxis = null;
		var ActiveShortcut = null;
		var axes_menu = null;
		var gradient_container = null;
		var add_axis_btn;
		var delete_axis_btn;
		var delete_point_btn;
		var update_output;
		var dragElem;

		var createStartAxis = function createStartAxis(angle) {

			if (ActiveAxis)
				ActiveAxis.deactivate();

			var axisID = getNextAxisID();
			var axis = new GradientAxis(gradient_container, axisID);
			var color = new HSVColor(210, 90, 90);
			ActiveAxis = axis;

			axis.activate();
			axis.setAxisAngle(angle);
			axis.newGradientPoint(10);
			axis.updatePointColor(color);

			color.setAlpha(0.5);
			axis.newGradientPoint(50);
			axis.updatePointColor(color);

			color.setHue(275);
			axis.newGradientPoint(50);
			axis.updatePointColor(color);

			color.setAlpha(1);
			axis.newGradientPoint(90);
			axis.updatePointColor(color);

			UIColorPicker.setColor('picker', color);
			lg_axes.push(axis);

			axis.Shortcut = createAxisShortcut(axisID);
			axis.activate();
		};

		var createAxis = function createAxis(angle) {

			if (ActiveAxis)
				ActiveAxis.deactivate();

			var axisID = getNextAxisID();
			var axis = new GradientAxis(gradient_container, axisID);
			var color = new HSVColor(0, 0, 50);
			ActiveAxis = axis;

			axis.activate();
			axis.setAxisAngle(angle);
			axis.newGradientPoint(10);
			axis.updatePointColor(color);

			color.setValue(90);
			axis.newGradientPoint(90);
			axis.updatePointColor(color);

			UIColorPicker.setColor('picker', color);
			lg_axes.push(axis);

			axis.Shortcut = createAxisShortcut(axisID);
			axis.activate();
		};

		var createAxisShortcut = function createAxisShortcut(axisID) {
			var axis = createClassElement('div', 'axis', axes_menu);

			axis.setAttribute('axisID', axisID);
			axis.setAttribute('draggable', 'true');
			axis.style.left = (lg_axes.length - 1) * 60 + 'px';

			axis.addEventListener('click', function() {
				activateAxisShortcut(axis);
				var axisID = this.getAttribute('axisID') | 0;
				activateAxis(axisID);
			});

			axis.addEventListener('dragstart', function (e) {
				dragElem = this;
				e.dataTransfer.setData('axisID', this.getAttribute('axisID'));
			});
			axis.addEventListener('dragover', allowDropEvent);
			axis.addEventListener('drop', function swap(e) {
				if (dragElem === this)
					return;

				var from = getOrderID(e.dataTransfer.getData('axisID'));
				var to = getOrderID(this.getAttribute('axisID'));

				var swap = lg_axes[from];
				lg_axes[from] = lg_axes[to];
				lg_axes[to] = swap;

				var left = dragElem.offsetLeft;
				dragElem.style.left = this.offsetLeft + 'px';
				this.style.left = left + 'px';

				updateCSSGradient();
			});

			activateAxisShortcut(axis);
			return axis;
		};

		var activateAxisShortcut = function activateAxisShortcut(node) {
			if (ActiveShortcut)
				ActiveShortcut.removeAttribute('data-state');
			node.setAttribute('data-state', 'active');
			ActiveShortcut = node;
		};

		var getNextAxisID = function getNextAxisID() {
			var ids = [];
			var idx = 0;
			var len = lg_axes.length;

			for (var i=0; i 1)
				delete_axis_btn.removeAttribute('data-state');
		};

		/* Axis functions */

		var updateAxisRotation = function updateAxisRotation(value) {
			ActiveAxis.setAxisAngle(value);
		};

		var setAxisUnit = function setAxisUnit(obj) {
			ActiveAxis.setUnit(obj.value);
		};

		var setAddAxisButton = function setAddAxisButton() {
			add_axis_btn = getElemById('add-axis');
			add_axis_btn.addEventListener('click', function() {
				if (lg_axes.length === 4)
					return;

				createAxis(0);

				if (lg_axes.length > 1)
					delete_axis_btn.removeAttribute('data-state');
				if (lg_axes.length === 4)
					this.setAttribute('data-state', 'disabled');
			});
		};

		var setDeleteAxisButton = function setDeleteAxisButton() {
			delete_axis_btn = getElemById('delete-axis');
			delete_axis_btn.addEventListener('click', function () {
				if (this.hasAttribute('data-state'))
					return;
				if (lg_axes.length === 1)
					return;

				axes_menu.removeChild(ActiveAxis.Shortcut);
				ActiveAxis.deleteAxis();
				lg_axes.splice(getOrderID(ActiveAxis.id), 1);

				ActiveAxis = null;
				updateCSSGradient();

				var len = lg_axes.length;
				for (var i=0; i
```

Dieses Werkzeug kann dazu verwendet werden, um benutzerdefinierte CSS3 {{cssxref("linear-gradient")}} Hintergründe zu erstellen.

{{EmbedLiveSample('linear-gradient_generator', '100%', '1000')}}