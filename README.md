# SKU

> ## 让我们直入主题
>
> ### 业务场景
>
> 在设计与实现商城项目中,大部分情况下都会涉及到用户购买商品的场景,在遇到商品拥有多种规格属性时,前端需要实现根据服务端返回的该项商品数据来进行不同情况的渲染。例如首先渲染该项商品的所有规格属性

```javascript
//Vue.js version 3.0+     <script setup>
import { reactive, onBeforeMount } from "vue";

const state = reactive({
  data: {},
  //模拟--把服务器端返回的数据 经过处理后的格式为如下:
  //例如 魅族18Pro 规格属性
  properties:[
    {
      name: "颜色", //规格
      attributes:
      [
        {
          value: "飞雪流光", //属性
          isActive: false, //当前状态是否点击
          isDisabled: false, //根据有无库存 实现点击状态是否禁用
        },
        {
          value: "银河秘境",
          isActive: false,
          isDisabled: false,
        },
        {
          value: "苍穹浩瀚",
          isActive: false,
          isDisabled: false,
        },
      ],
    },
    {
      name:'运行内存',
      attributes:
      [
        {
          value: "8G",
          isActive: false,
          isDisabled: false,
        },
        {
          value: "12G",
          isActive: false,
          isDisabled: false,
        },
      ],
    },
    {
      name:'内部存储',
      attributes:
      [
        {
          value: "128G",
          isActive: false,
          isDisabled: false,
        },
        {
          value: "256G",
          isActive: false,
          isDisabled: false,
        },
      ],
    }
  ],
  //假设魅族18Pro的库存情况如下
  skuArr:[
    {
      skuId:1001,
      attributes:['飞雪流光','12G','128G']
    },
    {
      skuId:1002,
      attributes:['苍穹浩瀚','8G','256G']
    },
  ],
  vertexArr:[],//顶点数组 vertexArr.length=7 本例商品所有规格属性的个数
  matrixArr:[],//矩阵数组  7×7
  selected:[],//当前已点击的规格属性数组
});
onBeforeMount(() => {
  //调用服务端API 获取商品数据
  state.data = res.data;
  //.....把后端的数据进行提取赋值
  //properties是该项商品下所有的规格属性
  //skuArr是该项商品下有库存的单元
});
```

> 如果我们使用根据 skuArr 循环匹配 properties 的话,规格类别越多我们需要嵌套循环的却多,这样的益处也不大。
> 观察下面的矩阵会不会有些灵感

<table>
  <tr>
    <th style="width:50px;height:50px;text-align:center"></th>
    <th style="width:50px;height:50px;text-align:center">飞雪</th>
    <th style="width:50px;height:50px;text-align:center">银河</th>
    <th style="width:50px;height:50px;text-align:center">苍穹</th>
    <th style="width:50px;height:50px;text-align:center;">8G</th>
    <th style="width:50px;height:50px;text-align:center">12G</th>
    <th style="width:50px;height:50px;text-align:center">128G</th>
    <th style="width:50px;height:50px;text-align:center">256G</th>
  </tr>
  <tr>
    <td style="width:50px;height:50px;text-align:center">飞雪</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">银河</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">苍穹</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">8G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">12G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">128G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">256G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
</table>

> 细数这是个 7×7 的矩阵 `计算机中也就是二维数组` ,这是因为在 properties 中我们一共有 7 个 value`规格属性`,我们把这 7 个规格属性组合成一个顶点数组即`vertexArr['飞雪','银河','苍穹','8G','12G','128G','256G']`.如果有 n 个`规格属性`,那么就是 n×n 的矩阵。当使用二维数组`matrixArr[][]`时明显我们可以使用`行+列` 两层 forEach 循环就可以匹配出想要的渲染情况。

```javascript
const initEmptyMatrixArr = () => {
  state.properties.forEach((property) => {
    property.attributes.forEach((attribute) => {
      state.vertexArr.push(attribute.value);
    });
  });
  for (let i = 0; i < state.vertexArr.length; i++) {
    state.matrixArr[i] = new Array(state.vertexArr.length).fill(0);
  }
};
// initEmptyMatrixArr() 初始化空的矩阵数组 每个坐标点为0
```

> 根据 skuArr 我们把有库存的坐标点设置为 1 结果就是下面这个矩阵

### matrixArr

<table>
  <tr>
    <th style="width:50px;height:50px;text-align:center"></th>
    <th style="width:50px;height:50px;text-align:center">飞雪</th>
    <th style="width:50px;height:50px;text-align:center">银河</th>
    <th style="width:50px;height:50px;text-align:center">苍穹</th>
    <th style="width:50px;height:50px;text-align:center">8G</th>
    <th style="width:50px;height:50px;text-align:center">12G</th>
    <th style="width:50px;height:50px;text-align:center">128G</th>
    <th style="width:50px;height:50px;text-align:center">256G</th>
  </tr>
  <tr>
    <td style="width:50px;height:50px;text-align:center">飞雪</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">银河</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">苍穹</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">8G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">12G</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">128G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">256G</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
</table>

```javascript
const initMatrixArr = () => {
  state.skuArr.forEach((sku) => {
    for (let i = 0; i < sku.attributes.length; i++) {
      sku.attributes.forEach((attribute, index) => {
        if (index != i) {
          state.matrixArr[state.vertexArr.indexOf(sku.attributes[i])][
            state.vertexArr.indexOf(attribute)
          ] = 1;
        }
      });
    }
  });
};
//initMatrixArr() 根据skuArr设置矩阵数组的坐标点
```

> 现在我们就可以根据上面这个矩阵数组进行`初步渲染`了,

```Html
<div v-for="(property, propertyIndex) in state.properties">
  <div>{{property.name}}</div>
  <div v-for="(attribute, attributeIndex) in property.attributes">
    <div :class="[attribute.isActive?'active-sku':'default-sku',attribute.isDisabled?'none-sku':'']" @click="handleClickAttribute(propertyIndex, attributeIndex)">{{attribute.value}}
    </div>
  </div>
</div>
```

```javascript
const handleClickAttribute = (propertyIndex, attributeIndex) => {
  //点击的某个单项规格属性
  const attribute = state.properties[propertyIndex].attributes[attributeIndex];
  // 若选项置灰,直接返回,表现为点击无响应
  if (attribute.isDisabled) {
    return;
  }
  //重复点击相同选项 重置每个 attribute 的 isActive 状态
  const isActive = !attribute.isActive;
  state.properties[propertyIndex].attributes[attributeIndex].isActive =
    isActive;
  //与此同时相同规格下的其他属性 isActive 状态改为 false
  if (isActive) {
    state.properties[propertyIndex].attributes.forEach((attr, index) => {
      if (index !== attributeIndex) {
        attr.isActive = false;
      }
    });
  }
  state.selected = [];
  state.properties.forEach((property: any) => {
    property.attributes.forEach((attr: any) => {
      if (attr.isActive) {
        state.selected.push(attr.value);
      }
    });
  });
  state.properties.forEach((prop: any) => {
    prop.attributes.forEach((attr: any) => {
      attr.isDisabled = !canAttributeSelect(attr);
    });
  });
};
const canAttributeSelect = (attribute: any) => {
  if (!state.selected || !state.selected.length || attribute.isActive) {
    return true;
  }
  let res = [];
  state.selected.forEach((value: any) => {
    const index1 = state.vertexArr.indexOf(value);
    const index2 = state.vertexArr.indexOf(attribute.value);
    res.push(state.matrixArr[index1][index2]);
  });
  //不可选中时坐标点值为0 否则坐标点值为1
  if (res.some((point) => point == 0)) {
    return false;
  } else if (res.some((point) => point == 1)) {
    return true;
  } else {
    const first = res[0];
    const others = res.slice(1);
    return Array.from(first).some((skuId) =>
      others.every((point) => point == skuId)
    );
  }
};
```

> 到此看起来已经实现了一个看着还不错的解法 🤔,但是此时我们又发现了一个问题,当点击同一层级规格下的属性时例如`颜色`,我们点击了其中一个属性选项后发现同级别下的其他属性却不可以点击了,这是因为我们的初始矩阵数组还没真正完善,[你看](#matrixarr)同级别下其他属性坐标点为 0,我们应该实现同层级下属性的切换(在有库存的情况下)即坐标点值为 1,如下

<table>
  <tr>
    <th style="width:50px;height:50px;text-align:center"></th>
    <th style="width:50px;height:50px;text-align:center">飞雪</th>
    <th style="width:50px;height:50px;text-align:center">银河</th>
    <th style="width:50px;height:50px;text-align:center">苍穹</th>
    <th style="width:50px;height:50px;text-align:center">8G</th>
    <th style="width:50px;height:50px;text-align:center">12G</th>
    <th style="width:50px;height:50px;text-align:center">128G</th>
    <th style="width:50px;height:50px;text-align:center">256G</th>
  </tr>
  <tr>
    <td style="width:50px;height:50px;text-align:center">飞雪</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">银河</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">苍穹</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">8G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">12G</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">128G</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
  </tr>
  <tr>
     <td style="width:50px;height:50px;text-align:center">256G</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">1</td>
    <td style="width:50px;height:50px;text-align:center">0</td>
  </tr>
</table>

> 所以我们要在 initMatrixArr()中添加如下代码

```javascript
const initMatrixArr = () => {
  state.skuArr.forEach((sku) => {
    for (let i = 0; i < sku.attributes.length; i++) {
      sku.attributes.forEach((attribute, index) => {
        if (index != i) {
          state.matrixArr[state.vertexArr.indexOf(sku.attributes[i])][
            state.vertexArr.indexOf(attribute)
          ] = 1;
        }
      });
    }
  });

  //++++++++++++新增的代码++++++++++++++++
  state.properties.forEach((property) => {
    for (let i = 0; i < property.attributes.length; i++) {
      property.attributes.forEach((attr, index) => {
        if (index != i) {
          state.matrixArr[
            state.vertexArr.indexOf(property.attributes[i].value)
          ][state.vertexArr.indexOf(attr.value)] = 1;
        }
      });
    }
  });
};
//initMatrixArr() 根据skuArr设置矩阵数组的坐标点
```

> 至此我们实现了一个还算能用的 SKU 算法 😜 其实它还能继续改进完善,剩下的就看你的发挥了！！
> 记得还有 实现你自己的 🤪 CSS 代码 o~
