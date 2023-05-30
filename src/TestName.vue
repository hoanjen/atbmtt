<template>
    <div>
        <input type="file" @change="handleFileInput">
        <div v-html="data"></div>
    </div>
</template>

<script>
import mammoth from 'mammoth';

export default {
    data(){
        return {
            data: null
        }
    },
    methods: {
        convertWordToHtml(file) {
            const reader = new FileReader();
            reader.onload = (event) => {
                const arrayBuffer = event.target.result;

                mammoth.extractRawText({ arrayBuffer: arrayBuffer })
                    .then((result) => {
                        const html = result.value;
                        // Sử dụng nội dung HTML được chuyển đổi
                        this.data = html
                        console.log(html);
                    })
                    .catch((error) => {
                        console.error('Lỗi chuyển đổi:', error);
                    });
            };

            reader.readAsArrayBuffer(file);
        },
        handleFileInput(event) {
            const file = event.target.files[0];
            if (file) {
                this.convertWordToHtml(file);
            }
        }
    }
};

</script>
