<template>
  <div class="ipfs-info">
    <h1>IPFS VUE 测试 demo</h1>
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
</template>

<script>
export default {
  name: "IpfsInfo",
  data: function() {
    return {
      status: "连接 IPFS 中...",
      id: "",
      agentVersion: "",
      hashCode:""
    };
  },
  mounted: function() {
    this.getIpfsNodeInfo();
  },
  methods: {
    async getIpfsNodeInfo() {
      try {
        const ipfs = await this.$ipfs;
        const { agentVersion, id } = await ipfs.id();
        this.agentVersion = agentVersion;
        this.id = id;
        this.status = "已经连接到 IPFS";
      } catch (err) {
        // Set error status text.
        this.status = `报错: ${err}`;
      }
    },
    // 上传文件到ipfs的方法
    async saveToIpfs(event) {
      let file = event.target.files[0]
      const ipfs = await this.$ipfs;
      try {
        const added = await ipfs.add(file, {
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

