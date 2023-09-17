<template>
  <div class="scan">
    <div class="scan-wrapper">
      <div class="textarea-box">
        <a-textarea
          v-model:value="startScanEvent.question"
          class="textarea"
          placeholder="Enter your input here......"
          :auto-size="{ minRows: 8, maxRows: 8 }"
        />
      </div>
      <div class="btn-group">
        <div class="btn" @click="startScanEvent.startScan">Submit</div>
      </div>
      <div class="scan-result">
        <div class="left">
          <div class="progress">
            <div class="progress-wrapper" :style="{height: startScanEvent.result.FraudPossibility || 0, minHeight:'16px'}">
              <div class="value">{{ startScanEvent.result.FraudPossibility }}</div>
            </div>
          </div>
        </div>
        <div class="right">
          <div class="result-detail" v-html="startScanEvent.result.email_text">
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
  import axios from "axios";
  import {reactive } from "vue";
  const startScanEvent = reactive({
    question: "",
    loading: false,
    result:{},
    startScan: ()=>{
      if(!startScanEvent.question){
        return;
      }
      const data = {
        content: startScanEvent.question
      }
      axios.get('/scan-api/email?content='+startScanEvent.question)
      .then(function (response) {
        console.log(response);
        startScanEvent.result = response.data
        if(startScanEvent.result.suspicious_sentence){
          //startScanEvent.result.highlightText = startScanEvent.result.email_text.replace(startScanEvent.result.suspicious_sentence,`<span class='active'>${tartScanEvent.result.suspicious_sentence}</span>`)
          const reg = new RegExp(`${startScanEvent.result.suspicious_sentence}`,'g');
          console.log(reg);
          startScanEvent.result.email_text = startScanEvent.result.email_text.replace(reg,`<span class='active'>${startScanEvent.result.suspicious_sentence}</span>`)
        }
        console.log(startScanEvent);
      }).catch((e)=>{
        console.log(e);
        startScanEvent.result = {};
      })
    }
  })
</script>
<style scoped>
  .scan{
    height: calc(100vh - 64px);
    background-color: #fff;
  }
  .scan-wrapper{
    padding:30px;
  }
  .textarea{
    padding:12px;
    background-color: #efefef;
  }
  .btn-group{
    width:100%;
    margin:20px 0;
  }
  .btn-group .btn{
    width: 200px;
    height: 50px;
    text-align: center;
    line-height: 50px;
    background-color: #135592;
    font-size:26px;
    font-weight: bold;
    margin:0 auto;
    color: #fff;
    border-radius: 25px;
    cursor: pointer;
  }
  .btn-group .btn:hover{
    background-color: #1862a7;
  }
  .scan-result{
    display: flex;
    height: 230px;
  }
  .progress{
    height: 230px;
    width: 148px;
    background-color: #d9d9d9;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
  }
  .progress-wrapper{
    height: 0%;
    background-color: #ef3c15;
    text-align: center;
    color: #fff;
  }
  .right{
    padding-left: 40px;
  }
  .right .result-detail{
    font-size: 24px;
    line-height: 32px;
    background-color: #efefef;
    width: calc(100vw - 280px);
    height: calc(100vh - 388px);
    padding:24px;
    border-radius: 24px;
    overflow: auto;
    word-break: break-all;
  }

</style>
<style>
  .right .result-detail .active{
    background-color: #ff6868;
    color: rgb(255, 255, 255);
    padding: 0 2px;
    border-radius: 2px;
  }
</style>