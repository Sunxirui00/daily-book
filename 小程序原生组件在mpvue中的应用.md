#### 单选radio-group

`<radio-group/>` 中的选中项发生变化时触发 change 事件，`event.mp.detail = {value: 选中项radio的value}`

```vue
<template>
  <div class="page">
    <radio-group @change="radioChange">
      <label class="weui-cell weui-check__label" v-for="item in radioItems" :key="index">
        <radio class="weui-check" :value="item.value" :checked="item.checked" />
        <div class="weui-cell__bd">{{item.name}}</div>
        <div class="weui-cell__ft weui-cell__ft_in-radio" v-if="item.checked">
          <icon class="weui-icon-radio" type="success_no_circle" size="16"></icon>
        </div>
      </label>
    </radio-group>
  </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      radioItems: [
        { name: 'cell standard', value: '0' },
        { name: 'cell standard', value: '1', checked: true }
      ],
    }
  },
  methods: {
    radioChange(e) {//每次值的改变都需要手动去更新data里的属性
      console.log('radio 携带的值为：' + e.mp.detail.value);
      let radioItems = this.radioItems;
      for (let i = 0; i < radioItems.length; ++i) {
        radioItems[i].checked = radioItems[i].value === e.mp.detail.value;
      }
      this.radioItems = radioItems;
    }
  }

}
</script>

<style>
.nones {
  color: red;
}
</style>
```

#### 栅格[Grid](https://kuangpf.com/mpvue-weui/#/grid?id=grid)

#### 多级联动 picker-view

`picker-view`+`picker-view-column`

```vue
<template>
<picker-view indicator-style="height: 40px;" :value="pickerValue" class="weui_picker_view" @change="pickerChange">
    <picker-view-column>
        <div class="picker-item" v-for="item in columuOne" :key="index">{{item}}</div>
    </picker-view-column>
    <picker-view-column>
        <div class="picker-item" v-for="item in columnTwo" :key="index">{{item}}</div>
    </picker-view-column>
</picker-view>
</template>
<script>
    export default {
        methods: {
            pickerChange(e) {
              let _this = this;
              let value = e.mp.detail.value;
              // 如果是第一列滚动
              if (value[0] !== _this.pickerValue[0]) {
                let columnTwoNew = _this.mulLinkAgeArray[value[0]].children;
                _this.columnTwo = [];
                for (let i = 0; i < columnTwoNew.length; i++) {
                  _this.columnTwo.push(columnTwoNew[i].label);
                }
                _this.pickerValue = value;
                _this.pickerValue[1] = 0;
              }
              // 如果第二列滚动
              if (value[1] !== this.pickerValue[1]) {
                _this.pickerValue[1] = e.mp.detail.value[1];
              }
              console.log('选中的值为：' + _this.mulLinkAgeArray[value[0]].label + '-' + _this.mulLinkAgeArray[value[0]].children[value[1]].label);
              console.log('pickerValue：' + this.pickerValue);
            },
    	}
    }
</script>
```

