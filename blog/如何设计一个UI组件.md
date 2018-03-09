# 如何设计一个UI组件

## 设计原则：松耦合、结构控制分离

## 设计过程
- 结构设计
- API设计
- 控制流

UI结构的设计，class命名遵循BEM规范，方便理解结构关系，减少class嵌套，增加性能

初学者的API设计和控制流容易杂糅在一起，用控制流直接的操作dom（在绑定事件中直接写操作dom的逻辑），会造成代码很乱，以后也不好更改和拓展。控制流可能经常变化，API相对稳定，也容易测试，所以API设计和控制流要分开且先设计API再设计控制流。

## 轮播图例子

### 结构设计

```javascript
<div id="my-slider" class="slider-list">
    <ul>
        <li class="slider-list__item--selected">
            <img src="https://p5.ssl.qhimg.com/t0119c74624763dd070.png"/>
        </li>
        <li class="slider-list__item">
            <img src="https://p4.ssl.qhimg.com/t01adbe3351db853eb3.jpg"/>
        </li>
        <li class="slider-list__item">
            <img src="https://p2.ssl.qhimg.com/t01645cd5ba0c3b60cb.jpg"/>
        </li>
        <li class="slider-list__item">
            <img src="https://p4.ssl.qhimg.com/t01331ac159b58f5478.jpg"/>
        </li>
    </ul>
</div>
```

```css
        #my-slider {
            margin: 0 auto;
            position: relative;
            height: 340px;
            width: 790px;
        }

        .slider-list ul {
            list-style-type: none;
            position: relative;
            width: 100%;
            height: 100%;
            padding: 0;
            margin: 0;
        }

        .slider-list__item,
        .slider-list__item--selected {
            position: absolute;
            transition: opacity 1s;
            opacity: 0;
        }

        .slider-list__item--selected {
            transition: opacity 1s;
            opacity: 1;
        }

```

### API设计

```javascript
class Slider {
        constructor(id, cycle = 2000) {
            const container = document.getElementById(id);
            const leftBtn = container.querySelector('.slider-control--left')
            const rightBtn = container.querySelector('.slider-control--right')
            const indicatesContainer = document.querySelector('.slider-indicates')
            this.indicates = container.querySelectorAll('.slider-indicates__item, .slider-indicates__item--selected');
            this.sliderItems = container.querySelectorAll('.slider-list__item, .slider-list__item--selected')
            this.currentIndex = 0
            this.cycle = cycle
            this.autoSlider = null
            this.slideHandlers=[]
        }

        doSlide(fn) {
            return (...args) => {
                this.stop()
                fn(...args)
                this.start()
            }
        }

        slideToNext() {

            let idx;
            if (this.currentIndex < this.sliderItems.length - 1) {
                idx = this.currentIndex + 1
            } else {
                idx = 0
            }
            this.slideTo(idx)
        }

        slideToLeft() {
            let idx;
            if (this.currentIndex > 0) {
                idx = this.currentIndex - 1
            } else {
                idx = this.sliderItems.length - 1
            }
            this.slideTo(idx)
        }

        slideTo(idx) {
            this.sliderItems[this.currentIndex].className = 'slider-list__item'
            this.sliderItems[idx].className = 'slider-list__item--selected'
            this.indicates[this.currentIndex].className = 'slider-indicates__item'
            this.indicates[idx].className = 'slider-indicates__item--selected'
            this.currentIndex = idx
            this.slideHandlers.forEach(handle=>{
                handle(idx+1)
            })
        }

        start() {
            this.autoSlider = setInterval(() => this.slideToNext(), this.cycle)
        }

        stop() {
            clearInterval(this.autoSlider)
        }

        addSlideListener(handle){
            this.slideHandlers.push(handle)
        }
    }
```
### 控制流设计

控制流结构
```javascript
    <div class="slider-indicates">
        <span class="slider-indicates__item--selected"></span>
        <span class="slider-indicates__item"></span>
        <span class="slider-indicates__item"></span>
        <span class="slider-indicates__item"></span>
    </div>

    <a href="#" class="slider-control slider-control--left">
        <
    </a>
    <a href="#" class="slider-control slider-control--right">
        >
    </a>
```

控制流CSS

```css
        .slider-indicates {
            position: relative;
            display: table;
            background-color: rgba(255, 255, 255, 0.5);
            padding: 5px;
            border-radius: 12px;
            bottom: 40px;
            margin: auto;
        }

        .slider-indicates__item,
        .slider-indicates__item--selected {
            display: inline-block;
            width: 15px;
            height: 15px;
            border-radius: 50%;
            margin: 0 5px;
            background-color: white;
            cursor: pointer;
        }

        .slider-indicates__item--selected {
            background-color: red;
        }

        .slider-control {
            position: absolute;
            top: 150px;
            text-decoration: none;
            color: #000;
            font-size: 36px;
        }

        .slider-control--right {
            right: 0;
        }

```

控制流JS
```javascript
            leftBtn.addEventListener('click', this.doSlide((e) => this.slideToLeft()))
            rightBtn.addEventListener('click', this.doSlide((e) => this.slideToNext()))
            indicatesContainer.addEventListener('click', this.doSlide((e) => {
                let idx = Array.from(this.indicates).indexOf(e.target)
                if (idx >= 0) {
                    this.slideTo(idx)
                }
            }))

```