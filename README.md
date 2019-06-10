<h1 align="center">Pure Vue Chart</h1>

<p align="center">Simple and lightweight vue chart component without chart library dependencies</p>

<hr/>

<p>A simple vue component for making charts that do not rely on large chart libraries and will not bloat your dependencies</p>

<h3> List of features </h3>

<ul>
  <li>Simple bar charts</li>
  <li>Line charts (planned)</li>
  <li>Pie charts (planned)</li>
  <li>Rose charts (planned)</li>
</ul>

<h3>Example</h3>

```
<pure-vue-chart
  :points="[3,5,2,5,4]"
  :width="400"
  :height="200"
/>
```

![](src/assets/charts.gif)

<h3> Download & Installation </h3>

<p>
To install:
</p>

```
$ npm i pure-vue-chart
```
<p>
Import it:
</p>

```
import PureVueChart from 'pure-vue-chart';
```
<p>
Use it:
</p>

```
components: {
    PureVueChart,
},
```
```
<pure-vue-chart
  :points="[3,5,2,5,4]"
  :width="400"
  :height="200"
/>
```
<h3>Contributing</h3>
I'm open to any issues or pull requests so long as
they are simple, easy to read, use the eslint settings in package.json, 
and follow commitizen-esque style commit formats

<h3>Authors or Acknowledgments</h3>
<ul>
  <li>Danny Jackson </li>
</ul>

<h3>License</h3>

This project is licensed under the MIT License
