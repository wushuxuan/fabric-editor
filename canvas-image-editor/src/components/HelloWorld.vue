<template>
    <div ref="tuiImageEditor" style="width: 100%;height: 100%;"></div>
</template>
<script>
import ImageEditor from 'tui-image-editor';
import { setTimeout } from 'timers';

const includeUIOptions = {
    includeUI: {
        initMenu: 'filter',
    }
};
const editorDefaultOptions = {
    cssMaxWidth: 700,
    cssMaxHeight: 500
};
export default {
    name: 'HelloWorld',
    props: {
        includeUi: {
            type: Boolean,
            default: true
        },
        options: {
            type: Object,
            default() {
                return editorDefaultOptions;
            }
        }
    },
    mounted() {
        let options = editorDefaultOptions;
        if (this.includeUi) {
            options = Object.assign(includeUIOptions, this.options);
        }
        this.editorInstance = new ImageEditor(this.$refs.tuiImageEditor, options);

        setTimeout(()=>{
            if(window.localStorage.getItem('record')){
                // console.log(JSON.parse(window.localStorage.getItem('record')))
                var list = JSON.parse(window.localStorage.getItem('record'));
                list.forEach(element => {
                    // console.log(element)
                    setTimeout(()=>{
                        this.editorInstance.addIcon(element.type, element).then(objectProps=>{
                            console.log(objectProps)
                        }).catch((res)=>{console.log(res)})
                    },500)
                });
            }

        },2000)


        this.addEventListener();
    },
    destroyed() {
        Object.keys(this.$listeners).forEach(eventName => {
            // console.log(eventName)
            this.editorInstance.off(eventName);
        });
        this.editorInstance.destroy();
        this.editorInstance = null;
    },
    methods: {
        addEventListener() {
            // console.log(this.$listeners)
            Object.keys(this.$listeners).forEach(eventName => {
                // console.log(eventName)
                this.editorInstance.on(eventName, (...args) => this.$emit(eventName, ...args));
            });
            
        },
        getRootElement() {
            return this.$refs.tuiImageEditor;
        },
        invoke(methodName, ...args) {
            // console.log(methodName)
            // console.log(...args)
            let result = null;
            if (this.editorInstance[methodName]) {
                result = this.editorInstance[methodName](...args);
            } else if (methodName.indexOf('.') > -1) {
                const func = this.getMethod(this.editorInstance, methodName);

                if (typeof func === 'function') {
                    result = func(...args);
                }
            }
            // console.log(result)
            return result;
        },
        getMethod(instance, methodName) {
            const {first, rest} = this.parseDotMethodName(methodName);
            const isInstance = (instance.constructor.name !== 'Object');
            const type = typeof instance[first];
            let obj;

            if (isInstance && type === 'function') {
                obj = instance[first].bind(instance);
            } else {
                obj = instance[first];
            }

            if (rest.length > 0) {
                return this.getMethod(obj, rest);
            }
            // console.log(obj)
            return obj;
        },
        parseDotMethodName(methodName) {
            // console.log(methodName)
            const firstDotIdx = methodName.indexOf('.');
            let firstMethodName = methodName;
            let restMethodName = '';

            if (firstDotIdx > -1) {
                firstMethodName = methodName.substring(0, firstDotIdx);
                restMethodName = methodName.substring(firstDotIdx + 1, methodName.length);
            }

            return {
                first: firstMethodName,
                rest: restMethodName
            };
        }
    }
};
</script>
