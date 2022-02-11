<template>
  <!-- <div id="app "> -->
    <!-- <div id="pdf-container"></div> -->
    <ul class="navbar" :class="{ hideNavBar }">
      <li @click="canvasToImage">
        <i class="fa fa-save"></i>
        儲存
      </li>
      <li @click="resetCanvas">
        <i class="fa fa-chalkboard"></i>
        清除
      </li>
      <li @click="back()">
        <i class="fa fa-undo"></i>
        上一步
      </li>

      <li @click="forward()">
        <i class="fas fa-redo"></i>
        下一步
      </li>
      <div class="btn up" @click="hideNavBar = !hideNavBar"></div>
    </ul>
    <div class="pdf-container">
      <canvas
        id="pdf-canvas"
        ref="sketchpad"
        @mousedown="onCanvasMouseDown"
        @mouseup="onCanvasMouseUp"
        :style="`background-color:${backgroundColor}; `"
      ></canvas>

      <ul class="toolbar" :class="{ hideToolBar }">
        <div
          class="btn btn-toolbar down"
          @click="hideToolBar = !hideToolBar"
        ></div>
        <li class="toolbar__tool">
          <i
            v-for="(tool, index) in tools"
            :key="index"
            :class="
              `${tool.preClass} fa-${tool.name} ` + isToolActive(tool.name)
            "
            @click="currentTool = tool.name"
          ></i>
        </li>
        <li class="toolbar__size">
          <span>SIZE:</span>
          <input
            type="text"
            @mousedown="onSizeMouseDown"
            @mouseup="resetToolState"
            v-model="currentSize"
            class="size-controller"
          />
          <span>px</span>
        </li>
        <li class="toolbar__color">
          <span>COLOR:</span>
          <div
            v-for="(color, index) in colors"
            class="color"
            :class="`color-${color.name} `+isColorActive(color.name)"
            :key="index"
            @click="currentColor = color"
          ></div>
        </li>
      </ul>
    </div>
  <!-- </div> -->
</template>
<script>
export default {
  name: 'app',
  data(){
    return {
      canvasContext:null,
      backgroundColor:'#dfdfdf',
      colors:[
        {name:'white',code:'white'},
        {name:'black',code:'black'},
        {name:'lightgreen',code:'#9BFFCD'},
        {name:'green',code:'#00CC99'},
        {name:'darkgreen',code:'#01936F'}],
      currentColor:null, 
      currentSize:'20',
      isSizing:false, 
      isCanvasMouseDown:false,
      hideToolBar:false,
      hideNavBar:false,
      tools:[
        {
          preClass:'fas',
          name:'paint-brush'
        },{
          preClass:'fas',
          name:'eraser'
        },{
          preClass:'far',
          name:'square'
        }],
      currentTool:'paint-brush',
      tempPosition:null,
      tempCanvas:null
    } 
  },
  methods:{

    onCanvasMouseDown(){ 
      this.isCanvasMouseDown = true
      this.setTempCanvas()
       
    },
    onCanvasMouseUp(){
      this.saveCanvasToHistory()
      this.resetToolState()
    },
    onSizeMouseDown(){ this.isSizing = true },
    canvasToImage(){
      let url =  this.$refs['sketchpad'].toDataURL("image/png", 1.0) 
      const link = document.createElement('a')
      link.innerText = 'Download'
      link.href = url 
      link.download = 'circle.png'     
      link.click()
    },
    setWindowEvent(){
      window.addEventListener('mousemove',(event)=>{
           
          let currentPos = this.getCanvasMousePosition(event.clientX,event.clientY)
          
          if(this.isSizing && this.tempPosition ){
            let dragValue= currentPos.y- this.tempPosition.y 
            this.checkSizeDrag(dragValue)
          }
          if(this.isCanvasMouseDown && this.tempPosition){
            let pos = this.getCanvasMousePosition(event.clientX,event.clientY)
            switch(this.currentTool){
              case 'paint-brush' : 
                    this.draw((ctx)=>{
                        ctx.strokeStyle =  this.currentColor.code
                        ctx.moveTo(this.tempPosition.x,this.tempPosition.y)
                        ctx.lineTo(pos.x,pos.y)
                    })
                    break;
              case 'eraser' :
                     this.draw((ctx)=>{
                      ctx.strokeStyle = this.backgroundColor
                      ctx.moveTo(this.tempPosition.x,this.tempPosition.y)
                      ctx.lineTo(pos.x,pos.y)
                      }) 
                    break;  
              case 'square' :
                    this.draw((ctx)=>{
                      // ctx.moveTo(this.tempPosition.x,this.tempPosition.y)
                      // ctx.lineTo(pos.x,pos.y)
                      let currentPos = this.getCanvasMousePosition(event.clientX,event.clientY)
                      
                      ctx.strokeStyle =  this.currentColor.code
                      let tempx = this.tempPosition.x
                      let tempy = this.tempPosition.y
                      let width = currentPos.x - tempx
                      let height = currentPos.y - tempy
                       
                      ctx.putImageData(this.tempCanvas,0, 0)                      
                      ctx.rect(tempx, tempy, width, height);
                    })
                    
                    break;  
            }
        }
        if(this.currentTool=='square' && this.isCanvasMouseDown )return
        this.setCanvasTempPosition(currentPos.x,currentPos.y)
          
         
      })
      window.addEventListener('popstate',(e)=>{
        this.canvasContext.putImageData(e.state, 0, 0);
      })
    },
    checkSizeDrag(dragValue){
      if(dragValue<0){ 
        this.currentSize =  parseInt(this.currentSize)+1
      }else if(dragValue>0 && this.currentSize>=1){
        this.currentSize = parseInt(this.currentSize)-1
      }
    },
    draw(action){
      let canvasContext = this.canvasContext
      canvasContext.beginPath()
      canvasContext.lineWidth = this.currentSize*2;        
      action(canvasContext)
      canvasContext.stroke()
    },
    resetToolState(){
      this.isSizing = false
      this.tempPosition = null
      this.isCanvasMouseDown = false
    }, 
    isColorActive(color){
      return this.currentColor && color==this.currentColor.name 
              ?'active' : ''
    },
    isToolActive(tool){
      return  tool == this.currentTool ? 'active' : ''
    },
    resetCanvas(){
      let canvas = this.canvasContext.canvas
      this.canvasContext.clearRect(0, 0, canvas.width, canvas.height);
      this.saveCanvasToHistory()
    },
    setCanvas(){
      let canvas = this.$refs['sketchpad']
      console.log(canvas);
      canvas.width  = window.innerWidth
      canvas.height  = window.innerHeight - 60
      let ctx =  canvas.getContext('2d')
      ctx.lineCap = "round"
      ctx.lineJoin = "round"
      this.canvasContext = ctx
    }, 
    setTempCanvas(){ 
      let ctx = this.canvasContext
      let canvas = ctx.canvas 
      let tempCanvas = ctx.getImageData(0, 0, canvas.width, canvas.height);
      this.tempCanvas = tempCanvas
    }, 
    saveCanvasToHistory(){
      let ctx = this.canvasContext
      let canvas = ctx.canvas 
      let tempCanvas = ctx.getImageData(0, 0, canvas.width, canvas.height);
      window.history.pushState(tempCanvas, null);
    },
    getCanvasMousePosition(clientX,clientY){
      let canvasPosition = this.canvasContext.canvas.getBoundingClientRect()
      let x = clientX-canvasPosition.x
      let y = clientY-canvasPosition.y
      return {x,y}
    },
    setCanvasTempPosition(x,y){
      this.tempPosition={x,y}
    },
    back(){
      console.log(window.history);
      window.history.back()
    },
    forward(){
      window.history.forward()
    }
  },
  mounted() {
    this.setCanvas()
    this.currentColor = this.colors[0]
    this.setWindowEvent()
  },
  
  
}
</script>