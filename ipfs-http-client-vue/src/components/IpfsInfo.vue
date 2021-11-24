<template>
  <div class="ipfs-info">
    <h1>ipfs-http-client vue 测试 demo</h1>
    <div v-if="!ipfs">
	    <h2>填写你的服务器链接</h2>
	    <input type="text" v-model="node">
	    <button @click="connect">连接ipfs</button>
	  </div>
    <div v-else>
      <img class="ipfs-logo" alt="IPFS logo" src="../assets/logo.svg" width="240"/>
      <h2>{{ status }}</h2>
      <h4>ID: {{ id }}</h4>
      <h4>Agent version: {{ agentVersion }}</h4>
      <br>
      <h2>上传文件</h2>
      <input type="file" class="add" @change="saveToIpfs"><br>
      <a v-show="hashCode" class="hash-link" :href="'https://ipfs.io/ipfs/'+hashCode">
        文件哈希值为：{{hashCode}}
      </a>
    </div>
  </div>
</template>

<script>
import IpfsHttpClient from 'ipfs-http-client'
export default {
  name: "IpfsInfo",
  data: function() {
    return {
      node:'/ip4/127.0.0.1/tcp/5001',
      ipfs:null,
      status: "连接 IPFS 中...",
      id: "",
      agentVersion: "",
      hashCode:""
    };
  },
  methods: {
    connect(){
      this.ipfs = IpfsHttpClient(this.node);
      this.getIpfsNodeInfo();
    },
    async getIpfsNodeInfo() {
      try {
        const { agentVersion, id } = await this.ipfs.id();
        this.agentVersion = agentVersion;
        this.id = id;
        this.status = "已经连接到 IPFS";
      } catch (err) {
        // Set error status text.
        this.status = `报错: ${err}`;
      }
    },
    // 上传文件到ipfs的方法
    async saveToIpfs() {
      let file = event.target.files[0]
      try {
        const added = await this.ipfs.add(file, {
          progress: (prog) => console.log(`received: ${prog}`),
        });

        // 获取上传文件hash值
        this.hashCode = added.cid.toString();

      } catch (err) {
        console.error(err);
      }
    }

  }
};
</script>

