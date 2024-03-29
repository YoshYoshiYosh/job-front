<template>
  <div>
    <button @click="enqueueJob">ジョブを開始</button>
    <div v-if="jobStatus">
      ジョブの状態: {{ jobStatus }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      jobStatus: null
    };
  },
  methods: {
    async enqueueJob() {
      try {
        const response = await this.$axios.post('/enqueue', { data: "myDataForJob"});
        const jobId = response.data.jobId;
        localStorage.setItem('jobId', jobId);
        // 成功した場合の処理をここに書く（例：ユーザーに通知する）
      } catch (error) {
        console.error('ジョブの開始に失敗しました', error);
      }
    },
    async checkJobStatus() {
      const jobId = localStorage.getItem('jobId');
      if (jobId) {
        try {
          // '/status/:jobId'エンドポイントにGETリクエストを送信してジョブの状態を確認
          const response = await this.$axios.get(`/status/${jobId}`);
          this.jobStatus = response.data;
        } catch (error) {
          console.error('ジョブの状態の確認に失敗しました', error);
        }
      }
    }
  },
  mounted() {
    this.checkJobStatus();
  }
}
</script>
