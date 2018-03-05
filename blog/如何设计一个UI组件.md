# 如何设计一个UI组件

## 设计原则：松耦合、结构控制分离

## 设计过程
- 结构设计
- API设计
- 控制流

UI结构的设计，class命名遵循BEM规范，方便理解结构关系，减少class嵌套，增加性能

初学者的API设计和控制流容易杂糅在一起，用控制流直接的操作dom（在绑定事件中直接写操作dom的逻辑），会造成代码很乱，以后也不好更改和拓展。控制流可能经常变化，API相对稳定，也容易测试，所以API设计和控制流要分开。

## 轮播图例子

### 结构设计

```
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
    <div class="slider-list__indicates">
        <span class="slider-list__indicates-item--selected"></span>
        <span class="slider-list__indicates-item"></span>
        <span class="slider-list__indicates-item"></span>
        <span class="slider-list__indicates-item"></span>
    </div>

    <a href="#" class="slider-list__control-buttons slider-list__control-buttons--left">
        <
    </a>
    <a href="#" class="slider-list__control-buttons slider-list__control-buttons--right">
        >
    </a>
</div>
```
