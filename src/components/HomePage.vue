<template>
  <el-container class="main-box">
    <el-row class="row">
      <el-col class="col" :span="24" :lg="12">
        <el-card class="box-card">
          <template #header>
            <div>
              <span>音频设置</span>
            </div>
          </template>
          <el-row class="p-10">
            <el-col class="select-label" :span="24" :sm="6">音色模型</el-col>
            <el-col :span="24" :sm="18">
              <el-select @change="confChange(3, 0)" v-model="selectDataList[0].value" class="w-100" placeholder="Select"
                size="large">
                <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-col>
          </el-row>
          <el-row v-for="(item, index) in audioConfigList" :key="index" class="p-10">
            <el-col class="select-label" :span="24" :sm="6">{{ item.label }}</el-col>
            <el-col :span="24" :sm="18">
              <el-slider @change="confChange(0, index)" v-model="item.value" :min="item.min" :max="item.max"
                class="w-100" :step="item.step" :disabled="item.disabled" show-stops show-input />
            </el-col>
          </el-row>
        </el-card>
      </el-col>
      <el-col class="col" :span="24" :lg="12">
        <el-card class="box-card">
          <template #header>
            <div>
              <span>对话设置</span>
            </div>
          </template>
          <el-row v-for="(item, index) in defaultPrompt" :key="index" class="p-10">
            <el-col class="select-label" :span="24" :sm="6">{{ item.label }}</el-col>
            <el-col :span="24" :sm="18">
              <el-input @change="confChange(2, index)" v-model="item.value" autosize type="textarea"
                placeholder="Please input" />
            </el-col>
          </el-row>

          <el-row v-for="(item, index) in openaiConfigList" :key="index" class="p-10">
            <el-col class="select-label" :span="24" :sm="6">{{ item.label }}</el-col>
            <el-col :span="24" :sm="18">
              <el-slider @change="confChange(1, index)" v-model="item.value" :min="item.min" :max="item.max"
                class="w-100" :step="item.step" :disabled="item.disabled" show-stops show-input />
            </el-col>
          </el-row>
        </el-card>

        <el-card style="margin-top: 20px;" class="box-card">
          <template #header>
            <div>
              <span>状态检测: {{ status_txt }}</span>
            </div>
          </template>
          <el-button class="button" type="danger" :disabled="!chatStatus" @click="stopChat" plain>关闭对话</el-button>
          <el-button class="button" type="primary" :disabled="chatStatus" @click="startChat" plain>启动对话</el-button>
        </el-card>


      </el-col>
    </el-row>

  </el-container>
</template>


<script>
import axios from 'axios'
import { h } from 'vue'
import { ElNotification } from 'element-plus'

export default {
  data() {
    return {
      options: [
        {
          value: 1,
          label: '度小宇',
        },
        {
          value: 0,
          label: '度小美',
        },
        {
          value: 3,
          label: '度逍遥（基础）',
        },
        {
          value: 4,
          label: '度丫丫',
        },
        {
          value: 5003,
          label: '度逍遥（精品）',
        },
        {
          value: 5118,
          label: '度小鹿',
        },
        {
          value: 106,
          label: '度博文',
        },
        {
          value: 5003,
          label: '度逍遥（精品）',
        },
        {
          value: 110,
          label: '度小童',
        }, {
          value: 111,
          label: '度小萌',
        }, {
          value: 103,
          label: '度米朵',
        }, {
          value: 5,
          label: '度小娇',
        }
      ],
      audioConfigList: [
        {
          'label': '触发阈值',
          'min': 100,
          'max': 10000,
          'value': 1000,
          'step': 100,
          'handler': 'audio',
          'name': 'audio_min_rms',
          'disabled': false
        }, {
          'label': '次数阈值',
          'min': 1,
          'max': 20,
          'value': 3,
          'handler': 'audio',
          'name': 'max_high_audio_flag',
          'disabled': false

        }, {
          'label': '忽略阈值',
          'min': 1,
          'max': 50,
          'value': 10,
          'handler': 'audio',
          'name': 'max_low_audio_flag',
          'disabled': false

        },
        {
          'label': '音调',
          'min': 1,
          'max': 10,
          'value': 1,
          'handler': 'baidu',
          'name': 'pit',
          'disabled': false

        },
        {
          'label': '语速',
          'min': 1,
          'max': 10,
          'value': 1,
          'handler': 'baidu',
          'name': 'spd',
          'disabled': false

        },
        {
          'label': '音量',
          'min': 1,
          'max': 10,
          'value': 1,
          'handler': 'baidu',
          'name': 'vol',
          'disabled': false

        }
      ],
      defaultPrompt: [
        {
          'label': '初始对话',
          'value': '',
          'handler': 'openai',
          'name': 'default_prompt_me',
          'disabled': false
        },
        {
          'label': '初始回应',
          'value': '',
          'handler': 'openai',
          'name': 'default_prompt_bot',
          'disabled': false
        }
      ],
      openaiConfigList: [
        {
          'label': '提交最大长度',
          'min': 128,
          'max': 1024,
          'value': 256,
          'handler': 'openai',
          'name': 'max_prompt_length',
          'disabled': false

        },
        {
          'label': '最大回复长度',
          'min': 12,
          'max': 256,
          'value': 128,
          'handler': 'openai',
          'name': 'openai_api_params',
          'disabled': false

        },
        {
          'label': '对话温度',
          'min': 0,
          'max': 1,
          'value': 0.6,
          'step': 0.1,
          'handler': 'openai',
          'name': 'openai_api_params',
          'disabled': false

        }
      ],
      selectDataList: [
        {
          'value': 4,
          'handler': 'baidu',
          'name': 'per',
          'disabled': false
        }
      ],
      status_txt: '',
      chatStatus: false,
      lastMsg: '',
    }
  },
  mounted() {
    this.initConfig()
    this.timer = setInterval(this.get_status, 1000)
  },
  beforeUnmount() {
    clearInterval(this.timer)
  },
  methods: {
    startChat() {
      axios.get('/start_chat').then(res => {
        if (res.status === 200) {
          ElNotification({
            title: '启动',
            message: h('i', { style: 'color: teal' }, '已发送启动命令'),
          })
        }
      })
    },
    stopChat() {
      axios.get('/stop_chat').then(res => {
        if (res.status === 200) {
          ElNotification({
            title: '停止',
            message: h('i', { style: 'color: teal' }, '已发送停止命令'),
          })
        }
      })
    },
    get_status() {
      axios.get('/chat_status').then(res => {
        console.log(res.data)
        if (res.data['status'] === 0) {
          this.status_txt = '运行'
          this.chatStatus = true
        } else {
          this.status_txt = '停止'
          this.chatStatus = false
        }
        if (res.data['msg'] !== '') {
          if (res.data['msg'] === this.lastMsg) {
            return
          }
          this.lastMsg = res.data['msg']
          if (res.data['msg'][0] === '你') {
            ElNotification({
              title: '你说',
              message: h('i', { style: 'color: CornflowerBlue' }, res.data['msg'].slice(3)),
            })
          } else if (res.data['msg'][0] === '猫') {
            ElNotification({
              title: '猫猫说',
              message: h('i', { style: 'color: OliveDrab' }, res.data['msg'].slice(4)),
            })
          }
        }
      })
    },
    initConfig() {
      axios.get('/get_config').then((response) => {
        console.log(response.data)
        let data = response.data

        let audio_conf = data['audio']
        this.audioConfigList[0].value = audio_conf['audio_min_rms']
        this.audioConfigList[1].value = audio_conf['max_high_audio_flag']
        this.audioConfigList[2].value = audio_conf['max_low_audio_flag']

        let baidu_conf = data['baidu']
        this.audioConfigList[3].value = baidu_conf['pit']
        this.audioConfigList[4].value = baidu_conf['spd']
        this.audioConfigList[5].value = baidu_conf['vol']

        this.value = baidu_conf['per']

        let openai_conf = data['openai']
        this.defaultPrompt[0].value = openai_conf['default_prompt_me']
        this.defaultPrompt[1].value = openai_conf['default_prompt_bot']

        this.openaiConfigList[0].value = openai_conf['max_prompt_length']
        this.openaiConfigList[1].value = openai_conf['openai_api_params']['max_tokens']
        this.openaiConfigList[2].value = openai_conf['openai_api_params']['temperature']
      })
    },
    confChange(type, index) {
      let dataList = []
      if (type === 0) {
        dataList = this.audioConfigList
      } else if (type === 1) {
        dataList = this.openaiConfigList
      } else if (type === 2) {
        dataList = this.defaultPrompt
      } else {
        dataList = this.selectDataList
      }


      dataList[index].disabled = true
      let handler = dataList[index].handler
      let name = dataList[index].name
      let value = dataList[index].value
      if (name === 'openai_api_params') {
        value = {
          frequency_penalty: 0.2,
          model: 'text-davinci-003',
          presence_penalty: 1,
          max_tokens: this.openaiConfigList[1].value,
          temperature: this.openaiConfigList[2].value,
          top_p: 1
        }
      }
      axios.post(
        '/set_config',
        {
          handler: handler,
          name: name,
          value: value
        }
      ).then((response) => {
        dataList[index].disabled = false
        console.log(response.data)
      })
    }
  }
}


</script>
<style>
.row {
  width: 100%;
}

.col {
  padding: 2%;
}

@media (max-width: 768px) {
  .main-box {
    text-align: left;
    padding-right: 10%;
    padding-left: 10%;
  }
}


@media (min-width: 768px) {
  .main-box {
    text-align: left;
    padding-right: 10%;
    padding-left: 10%;
  }
}
@media (max-width: 769px) {
  .main-box {
    text-align: left;
    padding-right: 0;
    padding-left: 0;
  }
}



.box-card {
  width: 100%;
}

.w-100 {
  width: 100%;
}

.p-10 {
  padding-left: 10%;
  padding-right: 10%;
  padding-top: 20px;
}

.select-label {
  line-height: 40px;
}
</style>
