<template>
    <div id="app">
    <div class="imageEditorApp">
      <tui-image-editor
        ref="tuiImageEditor"
        :include-ui="useDefaultUI"
        :options="options"
        @addText="onAddText"
        @objectMoved="onObjectMoved"
        @objectScaled="onObjectScaled"
        @redoStackChanged="onRedoStackChanged"
         @undoStackChanged="onUndoStackChanged"
        @objectActivated="objectActivated"
      ></tui-image-editor>
    </div>
  </div>
</template>

<script>
import "tui-image-editor/dist/svg/icon-a.svg";
import "tui-image-editor/dist/svg/icon-b.svg";
import "tui-image-editor/dist/svg/icon-c.svg";
import "tui-image-editor/dist/svg/icon-d.svg";

// Load Style Code
import "tui-image-editor/dist/tui-image-editor.css";
import "tui-color-picker/dist/tui-color-picker.css";

import $ from "jquery";
import HelloWorld from "./components/HelloWorld.vue";
import bgpng from "./assets/img/bg.png";
import iconA from "./assets/icon-a.svg";
import iconB from "./assets/icon-b.svg";
import iconC from "./assets/icon-c.svg";
import iconD from "./assets/icon-d.svg";
import imageUrl from "./assets/img/thanksgiving-3811492_960_720.jpg";

var locale_ru_RU = {
  // override default English locale to your custom
  "Crop": "裁剪",
  "Load": "上传图片",
  "Shape": "图形",
  "Circle":"圆",
  "Delete-all": "全部清空"
  // etc...
};


export default {
  name: "app",
  components: {
    "tui-image-editor": HelloWorld
  },
  data() {
    return {
      useDefaultUI: true,
      options: {
        includeUI: {
          initMenu: "filter",
           locale: locale_ru_RU,
          loadImage: {
            path: imageUrl,
            name: "demoImage"
          },
          menuBarPosition: "left",
          // menu: ["crop", "shape", "icon", "text", "mask", "filter"],
          theme: {
            "common.bi.image": "",
            "common.bisize.width": "251px",
            "common.bisize.height": "21px",
            "common.backgroundImage": bgpng,
            "common.backgroundColor": "#fff",
            "common.border": "1px solid #c1c1c1",

            // header
            "header.backgroundImage": "none",
            "header.backgroundColor": "transparent",
            "header.border": "0px",

            // load button
            "loadButton.backgroundColor": "#fff",
            "loadButton.border": "1px solid #ddd",
            "loadButton.color": "#222",
            "loadButton.fontFamily": "'Noto Sans', sans-serif",
            "loadButton.fontSize": "12px",

            // download button
            "downloadButton.backgroundColor": "#fdba3b",
            "downloadButton.border": "1px solid #fdba3b",
            "downloadButton.color": "#fff",
            "downloadButton.fontFamily": "'Noto Sans', sans-serif",
            "downloadButton.fontSize": "12px",

            // main icons
            "menu.normalIcon.path": iconD,
            "menu.normalIcon.name": "icon-d",
            "menu.activeIcon.path": iconB,
            "menu.activeIcon.name": "icon-b",
            "menu.disabledIcon.path": iconA,
            "menu.disabledIcon.name": "icon-a",
            "menu.hoverIcon.path": iconC,
            "menu.hoverIcon.name": "icon-c",
            "menu.iconSize.width": "24px",
            "menu.iconSize.height": "24px",

            // submenu primary color
            "submenu.backgroundColor": "transparent",
            "submenu.partition.color": "#e5e5e5",

            // submenu icons
            "submenu.normalIcon.path": iconD,
            "submenu.normalIcon.name": "icon-d",
            "submenu.activeIcon.path": iconB,
            "submenu.activeIcon.name": "icon-b",
            "submenu.iconSize.width": "32px",
            "submenu.iconSize.height": "32px",

            // submenu labels
            "submenu.normalLabel.color": "#858585",
            "submenu.normalLabel.fontWeight": "normal",
            "submenu.activeLabel.color": "#000",
            "submenu.activeLabel.fontWeight": "normal",

            // checkbox style
            "checkbox.border": "1px solid #ccc",
            "checkbox.backgroundColor": "#fff",

            // rango style
            "range.pointer.color": "#333",
            "range.bar.color": "#ccc",
            "range.subbar.color": "#606060",

            "range.disabledPointer.color": "#d3d3d3",
            "range.disabledBar.color": "rgba(85,85,85,0.06)",
            "range.disabledSubbar.color": "rgba(51,51,51,0.2)",

            "range.value.color": "#000",
            "range.value.fontWeight": "normal",
            "range.value.fontSize": "11px",
            "range.value.border": "0",
            "range.value.backgroundColor": "#f5f5f5",
            "range.title.color": "#000",
            "range.title.fontWeight": "lighter",

            // colorpicker style
            "colorpicker.button.border": "0px",
            "colorpicker.title.color": "#000"
          }
        },
        cssMaxWidth: 700,
        cssMaxHeight: 500
      }
    };
  },
    mounted() {
    console.log(
      $(".tui-image-editor-header-buttons .tui-image-editor-download-btn")
    );
    $(
      ".tui-image-editor-header-buttons .tui-image-editor-download-btn"
    ).replaceWith(
      '<button class="tui-image-editor-save-btn" style="background-color: #fdba3b;border: 1px solid #fdba3b; color: #fff;font-size: 12px;">保存</button>'
    );
    document.querySelector(".tui-image-editor-header-buttons .tui-image-editor-save-btn" ).addEventListener("click", this.save);


    window.onmousewheel = function (){
      console.log("鼠标滚动")
    }

  },
  methods: {
    onAddText(res) {
      console.log("RES : ", res);
    },
    //移动
    onObjectMoved(res) {
       this.getRecord(res)
    },
    //新增/选中
    objectActivated(res) {
       this.getRecord(res)
    },
    //缩放
    onObjectScaled(res) {
      this.getRecord(res)
    },
    //重做
    onRedoStackChanged(res) {
      console.log("RES : ", res);
    },
    onUndoStackChanged(res) {
      if(res == 0){
        this.records = []
      }
    },
    getRecord(res){
    var flag = false;
      for (let index = 0; index < this.records.length; index++) {
        const element ={
          fill: this.records[index].fill,
          height: this.records[index].height,
          id: this.records[index].id,
          left: this.records[index].left,
          opacity: this.records[index].opacity,
          stroke: this.records[index].stroke,
          strokeWidth: this.records[index].strokeWidth,
          top: this.records[index].top,
          type: this.records[index].type,
          width: this.records[index].width,
        };
        if(element.id == res.id){
          // console.log(element)
          this.records[index] = res;
          flag = true;
        }
      }
      if(!flag){
        this.records.push(res)
      }
      // console.log(this.records)
    },
    //
    save() {
      let res = {};
      //   console.log(this.$refs.tuiImageEditor.editorInstance._invoker)
      // console.log(this.$refs.tuiImageEditor.editorInstance._invoker._undoStack)"loadImage"
      var list =[];
      this.$refs.tuiImageEditor.editorInstance._invoker._undoStack.forEach(element => {
                  // console.log(element.name)
        this.records.forEach(item => {
          if(element.name =='addIcon' && element.undoData.object.__fe_id && item.id == element.undoData.object.__fe_id){
            item.type = element.args[1]
          }
        });
      });
      var deleteId = [];
       this.$refs.tuiImageEditor.editorInstance._invoker._undoStack.forEach(element => {
         if(element.name == "removeObject"){
             deleteId.push(element.args[1])
         }
       })
       this.records.forEach(item => {
          if(deleteId.indexOf((item.id).toString())>=0){
              console.log(" ")
          }else{
            list.push(item)
          }
        });
        //全清楚
          this.$refs.tuiImageEditor.editorInstance._invoker._undoStack.forEach(element => {
            if(element.name == "loadImage" || element.name ==  "clearObjects"){
              list = []
            }
          })
      const image = this.$refs.tuiImageEditor.editorInstance.toDataURL();
      res.url = image;
      res.records = list;
       console.log("最后结果：")
      console.log(res)
    //  console.log(this.records)
     window.localStorage.setItem('record',JSON.stringify(list))
    },

  }
};
</script>
<style scoped>
.imageEditorApp {
  width: 1000px;
  height: 800px;
}
.tui-image-editor-header-logo {
  display: none !important;
  border: 1px solid red;
}
.tui-image-editor-download-btn {
  display: none !important;
}
</style>
