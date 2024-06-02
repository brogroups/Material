<template>
  <div>
    <input type="file" :multiple="multifile" accept=".json" @change="handleFileUpload">
    <ul v-if="uploadedFiles.length">
      <li v-for="file in uploadedFiles" :key="file.name">{{ file.name }}</li>
    </ul>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  props: {
    multifile: {
      type: Boolean,
      default: false
    },
    limit: {
      type: Number,
      default: 1
    },
    fileType: {
      type: String,
      default: '.json'
    }
  },
  setup(props) {
    const uploadedFiles = ref([]);
    const allowedTypes = props.fileType.split(',');

    const handleFileUpload = (event) => {
      const files = event.target.files;
      const allowedFiles = [];

      for (let i = 0; i < files.length; i++) {
        const file = files[i];
        const extension = file.name.split('.').pop();
        if (allowedTypes.includes('.' + extension)) {
          allowedFiles.push(file);
        } else {
          console.error(`Unsupported file type: ${extension}`);
        }
      }

      if (allowedFiles.length > 0) {
        if (props.multifile) {
          const remainingSpace = props.limit - uploadedFiles.value.length;
          const toUpload = allowedFiles.slice(0, remainingSpace);
          uploadedFiles.value = [...uploadedFiles.value, ...toUpload];
        } else {
          uploadedFiles.value = [allowedFiles[0]];
        }
      }
    };

    return {
      handleFileUpload,
      uploadedFiles
    };
  }
};
</script>
