<template>
  <div class="main" v-for="(property, propertyIndex) in state.properties">
    <div>{{ property.name }}</div>
    <div class="skuArr">
      <div
        class="sku"
        v-for="(attribute, attributeIndex) in property.attributes"
        @click="handleClickAttribute(propertyIndex, attributeIndex)"
        :class="[
          attribute.isActive ? 'active-sku' : 'default-sku',
          attribute.isDisabled ? 'none-sku' : '',
        ]"
      >
        {{ attribute.value }}
      </div>
    </div>
  </div>
</template>
<script setup>
//Vue.js version 3.0+     <script setup>
import { reactive, onBeforeMount } from "vue";

const state = reactive({
  data: {},
  //模拟--把服务器端返回的数据 经过处理后的格式为如下:
  //例如 魅族18Pro 规格属性
  properties: [
    {
      name: "颜色", //规格
      attributes: [
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
      name: "运行内存",
      attributes: [
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
      name: "内部存储",
      attributes: [
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
    },
  ],
  //假设魅族18Pro的库存情况如下
  skuArr: [
    {
      skuId: 1001,
      attributes: ["飞雪流光", "12G", "128G"],
    },
    {
      skuId: 1002,
      attributes: ["苍穹浩瀚", "8G", "256G"],
    },
  ],
  vertexArr: [], //顶点数组 vertexArr.length=7 本例商品所有规格属性的个数
  matrixArr: [], //矩阵数组  7×7
  selected: [], //当前已点击的规格属性数组
});
onBeforeMount(() => {
  //调用服务端API 获取商品数据
  // state.data = res.data;
  //.....把后端的数据进行提取赋值
  //properties是该项商品下所有的规格属性
  //skuArr是该项商品下有库存的单元
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
  initEmptyMatrixArr();
  initMatrixArr();
});
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
  state.properties.forEach((property) => {
    property.attributes.forEach((attr) => {
      if (attr.isActive) {
        state.selected.push(attr.value);
      }
    });
  });
  state.properties.forEach((prop) => {
    prop.attributes.forEach((attr) => {
      attr.isDisabled = !canAttributeSelect(attr);
    });
  });
};
const canAttributeSelect = (attribute) => {
  if (!state.selected || !state.selected.length || attribute.isActive) {
    return true;
  }
  let res = [];
  state.selected.forEach((value) => {
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
</script>

<style scoped>
.main {
  width: 700px;
  font-size: 24px;
  font-weight: 550;
  margin: 40px auto;
}
.skuArr {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  gap: 50px;
  height: 70px;
  margin: 50px 20px;
}
div {
  border-radius: 10px;
}
.sku {
  font-size: 24px;
  font-weight: 400;
  width: fit-content;
  height: 50px;
  padding: 0 20px;
  line-height: 50px;
  transform: scale(1);
  transition: transform 1s;
}

.default-sku {
  color: #797979;
  color: transparent;
  box-shadow: 0px 0px 12px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  -webkit-text-stroke: 0.2px #797979;
  -webkit-background-clip: text;
  transition: box-shadow 1s, color 1s;
}

.none-sku {
  color: #d2cfcf;
  cursor: no-drop;
  transform: none;
  box-shadow: none;
  -webkit-text-stroke: 0.2px #d2cfcf;
  -webkit-background-clip: text;
  transition: box-shadow 1s, color 1s;
}
.none-sku:hover {
  transform: none;
  box-shadow: none;
}
.active-sku {
  --color: #2edae0;
  --w1: radial-gradient(100% 57% at top, #0000 100%, var(--color) 100.5%)
    no-repeat;
  --w2: radial-gradient(100% 58% at bottom, var(--color) 100%, #0000 100.5%)
    no-repeat;
  background: var(--w1), var(--w2), var(--w1), var(--w2);
  background-position: -200% 100%, -100% 100%, 0 100%, 100% 100%;
  box-shadow: 0px 0px 12px rgba(46, 218, 224, 0.8);
  background-size: 50.5% 100%;
  /* border: 1px solid #2edae0; */
  cursor: pointer;
  animation: m 1s infinite linear;

  font-weight: bold;
  color: transparent;
  -webkit-text-stroke: 0.2px var(--color);
  -webkit-background-clip: text;
  text-shadow: 0 0 150px var(--color);
  text-align: center;

  transition: border 1s, text-shadow 1s, color 1s, box-shadow 1s,
    -webkit-text-stroke 1s, -webkit-background-clip 1s, background-position 1s,
    background-size 1s;
}
@keyframes m {
  0% {
    background-position-x: -200%, -100%, 0%, 100%;
    transform: translateY(0%);
  }

  50% {
    transform: translateY(10%);
  }

  100% {
    background-position-x: 0%, 100%, 200%, 300%;
    transform: translateY(0%);
  }
}
</style>
