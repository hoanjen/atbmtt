<template>
    <div>
        <input type="file" @change="handleFileInput">
        <div id="container" style="margin:10px;padding:10px;border:1px solid; text-align:left; min-height:500px;"></div>
    </div>
</template>


<script>
import Docx2Html from 'docx2html';

export default {
    methods: {
        test(input){
			Docx2Html(input,{container:document.querySelector("#container")})
				.then(html=>{
					input.value=""
					console.log(html)
					//try html.toString/asZip/download/save
				})
		},
        handleFileInput(event) {
            const file = event.target.files[0];
            if (file) {
                this.test(file);
            }
        },
    },
};



</script>
