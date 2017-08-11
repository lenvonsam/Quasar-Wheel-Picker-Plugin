# Quasar Wheel Picker Plugin V1.5

> A Quasar framework plugin 

## Wheel Picker Types & screenshots

* ####  Basic Picker

* #### Max Group Picker(less then 3 group count)

* #### City Picker(ZH) — update to 2016

* #### Date Picker

> ##### Basic Picker's screenshot V1.0 & V1.5

![Alt baiscpicker](screenshots/basicpicker.png)  ![Alt basicpicker1.5](screenshots/basicpicker1_5.png)

> ##### Max Group Picker's screenshot V1.0 & V1.5

![Alt maxpicker](screenshots/maxpicker.png)  ![Alt maxpicker1.5](screenshots/maxpicker1_5.png)

> ##### City Picker's screenshot V1.0 & V1.5

![Alt citypicker](screenshots/citypicker.png) ![Alt citypicker1.5](screenshots/citypicker1_5.png)

> ##### Date Picker's screenshot V1.0 & V1.5

![Alt datepicker](screenshots/datepicker.png) ![Alt datepicker1.5](screenshots/datepicker1_5.png)

## Live Demo
[Experience](https://lenvonsam.github.io/Quasar-Wheel-Picker-Plugin/)

![Alt qcode](screenshots/qcode.png)

## How to Use
1. Install quasar-cli
``` bash
$ npm install -g quasar-cli
```
2. Install pug dependency
``` bash
$ npm install pug pug-loader pug-filters -D
```
after install pug,find build/webpack.base.conf.js, then add block code like this:

![Alt pugdep](screenshots/pugdep.png)

3. Copy src/components/picker fold to your project

4. Import picker plugin

![Alt importpicker](screenshots/imporpicker.png)

5. Instance components

![Alt components](screenshots/components.png)

6. Using components

![Alt dompicker](screenshots/dompicker.png)

## Build Setup Demo project

``` bash
# install dependencies
$ npm install

# serve with hot reload at localhost:8080
$ quasar dev

# build for production with minification
$ quasar build

# lint code
$ quasar lint
```

## ChangeLog
### ~ Version 1.5
* every picker page extends five pickerItems
* optimized picker UI
* update China citypicker data to 2016(There is any mistake to welcome correction)
