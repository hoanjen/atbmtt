
<template>
  
  <div class="z-10 w-[1200px] h-[900px] text-white m-auto text-center">
    <div class="h-[200px] bg-slate-400 flex text-red-400">
      <div class="w-[300px] bg-slate-200 mr-10">
        <div>Khóa bí mật</div>
        <div>
          <span class="mr-2">a:</span>
          <input v-model="a" type="number">
        </div>
        <div class="mt-2">
              <span class="mr-2">k:</span>
              <input v-model="k" type="number">
        </div>
      </div>
      <div class="w-[300px] bg-slate-200">
        <div>Khóa công khai</div>
        <div class="my-2">
          <span class="mr-2">p:</span>
          <input v-model="p" type="number">
        </div>
        <div>
          <span class="mr-2">α:</span>
          <input v-model="alpha" type="number">
        </div>
        <div class="my-2">
          <span class="mr-2">β:</span>
          <input v-model="beta" type="number">
        </div>
      </div>
      <div class="ml-5 mt-20 w-26 p-2 bg-emerald-400 rounded-lg cursor-pointer  max-h-10" @click="sinh">Sinh tự động</div>
    </div>
    <div class="flex h-[700px]">
      <div class="w-[600px] bg-gray-500">
        <div class="m-5">
          <div class="mb-5">Phát sinh chữ ký</div>
          <div class="flex">
            <div class="mr-5" >Văn bản ký</div>
            <input v-if="isTxT1" class="w-60 h-32 bg-slate-400" ref="input1a"  v-model="vanbanky" type="text">
            <div v-if="!isTxT1" id="container" class="max-w-60 max-h-32 "></div>
            <div class="w-60 h-32">
              <label class="block ">
                <span class="cursor-pointer sr-only">Chọn file</span>
                <input type="file" @change="readFile1" ref="input1b" class="w-full cursor-pointer text-sm text-red-300
                file:mr-4 file:py-2 file:px-4
                file:rounded-full file:border-0
                file:text-sm file:font-semibold
                file:bg-violet-50 file:text-violet-700
                hover:file:bg-violet-100
                    "/>
              </label>
              <span class="cursor-pointer text-white" @click="inputDefault1">Bỏ chọn file</span>
            </div>
          </div>
          <div class="ml-44 my-5 p-2 w-16 text-center bg-emerald-400 rounded-lg cursor-pointer " @click="hamky">Ký</div>
          <div class="flex">
            <div class="mr-12">Chữ ký</div>
            <input class="w-60 h-32 bg-slate-400" v-model="chuky" type="text">
            <div class="flex flex-col ml-5">
              <div class="bg-emerald-400 rounded-lg cursor-pointer p-1 py-2 w-16 mb-5" @click="chuyen">
                Chuyển
              </div>
              <div class="bg-emerald-400 rounded-lg cursor-pointer p-2 w-16" @click="saveTextAsFile">
                Lưu
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="w-[600px] bg-gray-500 border-l-2">
        <div class="m-5">
          <div class="mb-5">Kiểm tra chữ ký</div>
          <div class="flex">
            <div class="mr-5">Văn bản ký</div>
            <input v-if="isTxT2" class="w-60 h-32 bg-slate-400" ref="input2a" v-model="vanbanky2" type="text">
            <div v-if="!isTxT2" id="containerr" class="max-w-60 max-h-32 "></div>
            <div>
              <label class="block ">
                    <span class="cursor-pointer sr-only">Chọn file</span>
                    <input type="file" @change="readFile2" ref="input2b" class="w-full cursor-pointer text-sm text-red-300
                  file:mr-4 file:py-2 file:px-4
                  file:rounded-full file:border-0
                  file:text-sm file:font-semibold
                  file:bg-violet-50 file:text-violet-700
                  hover:file:bg-violet-100
                      "/>
              </label>
              <span class="cursor-pointer text-white block" @click="inputDefault2">Bỏ chọn file</span>
            </div>
          </div>
          <div class="flex mt-10">  
            <div class="mr-12">Chữ ký</div>
            <input class="w-60 h-32 bg-slate-400" v-model="chuky2" type="text">
            <div class="">
              <label class="block ">
                    <span class="cursor-pointer sr-only">Chọn file</span>
                    <input type="file" @change="readFile3" class="w-full cursor-pointer text-sm text-red-300
                file:mr-4 file:py-2 file:px-4
                file:rounded-full file:border-0
                file:text-sm file:font-semibold
                file:bg-violet-50 file:text-violet-700
                hover:file:bg-violet-100
                    "/>
              </label>
            </div>
          </div>
          <div class="ml-36 m-5 p-2 w-32 text-center bg-emerald-400 rounded-lg cursor-pointer " @click="xacnhan">Kiểm tra
            chữ ký</div>
          <div class="flex">
            <div class="mr-5">Thông báo</div>
            <input class="w-60 h-32 bg-slate-400" v-model="thongbao" type="text">
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import bigInt from 'big-integer';
import { SHA256 } from 'crypto-js'
import Docx2Html from 'docx2html';
import { Document, Packer, Paragraph, TextRun } from "docx";
import mammoth from "mammoth";
export default {
  data() {
    return {
      vanbanky: '',
      chuky: '',
      vanbanky2: '',
      chuky2: '',
      p: null,
      alpha: null,
      beta: null,
      a: null,
      k: null,  
      gamma: null,
      delta: null,
      thongbao: null,
      file: null, 
      content: null,
      isTxT1: true,
      isTxT2: true,
      datatransferinput: null
    }
  },
  methods: {
    inputDefault1(){
      if(this.$refs.input1a !== null){
        this.$refs.input1a.value = null
      }
      this.$refs.input1b.value = null
      this.vanbanky = null
      this.isTxT1 = true
      this.datatransferinput = null
    },
    inputDefault2() {
      if (this.$refs.input2a !== null) {
        this.$refs.input2a.value = null
      }
      this.$refs.input2b.value = null
      this.vanbanky2 = null
      this.isTxT2 = true
    },
    test1(input) {
      Docx2Html(input, { container: document.querySelector("#container") })
        .then(html => {
          this.vanbanky = html.toString()
        })
    },
    test2(input) {
      Docx2Html(input, { container: document.querySelector("#containerr") })
        .then(html => {
          this.vanbanky2 = html.toString()
        })
    },
    readFile1(event) {
        const file = event.target.files[0]
        const reader = new FileReader()
        let arr = file.name.split('.')
        if(arr[arr.length-1] === 'txt'){
          reader.readAsText(file, 'utf8')
          reader.addEventListener('load', (e) => {
            this.vanbanky = e.target.result;
          })
        } else if(arr[arr.length - 1] === 'docx'){
          this.isTxT1 = false
          this.datatransferinput = file
          setTimeout( () => this.test1(file),20)
        }
    },
    readFile2(event) {
      const file = event.target.files[0]
      const reader = new FileReader()
      let arr = file.name.split('.')
      if (arr[arr.length - 1] === 'txt') {
        reader.readAsText(file, 'utf8')
        reader.addEventListener('load', (e) => {
          this.vanbanky2 = e.target.result;
        })
      } else if (arr[arr.length - 1] === 'docx') {
        this.isTxT2 = false
        setTimeout(() => this.test2(file), 20)
      }
    },
    readFile3(event) {
      const file = event.target.files[0]
      const reader = new FileReader()
      let arr = file.name.split('.')
      if(arr[arr.length - 1] === 'txt'){
  
        reader.readAsText(file, 'utf8')
        reader.addEventListener('load', (e) => {
          this.chuky2 = e.target.result;
        })
      }
      else{
          reader.onload = (e) => {
          const arrayBuffer = e.target.result;

          // Đọc file .docx và chuyển đổi thành văn bản
          mammoth.extractRawText({ arrayBuffer: arrayBuffer })
            .then((result) => {
              const text = result.value; // Văn bản đã chuyển đổi
              console.log(text);
              this.chuky2 = text
            })
            .catch((error) => {
              console.error('Lỗi khi đọc file:', error);
            });
        };

        reader.readAsArrayBuffer(file);
      }
    },
    saveTextAsFile() {
      if(this.datatransferinput === null){
        const text = this.chuky;
        const filename = "chuky.txt";
  
        const blob = new Blob([text], { type: "text/plain" });
        const url = URL.createObjectURL(blob);
  
        const link = document.createElement("a");
        link.href = url;
        link.download = filename;
        link.click();
  
        URL.revokeObjectURL(url);
      } else{

        const doc = new Document({
          sections: [
            {
              properties: {},
              children: [
                new Paragraph({
                  children: [
                    new TextRun({
                      text: this.chuky,
                      size: 40
                    })
                  ],
                }),
              ],
            },
          ],
        });

        Packer.toBlob(doc).then((blob) => {
          const url = URL.createObjectURL(blob);
          const link = document.createElement("a");
          link.href = url;
          link.download = "chuky.docx";
          link.click();
          URL.revokeObjectURL(url);

        });
      }
    },
    sinh() {
      this.p = this.randomNT(100000, 900000);
      this.alpha = Math.floor(Math.random(0, 1)*(this.p - 2) + 1)
      this.a = Math.floor(Math.random(0, 1) * (this.p - 4) + 2)
      this.beta = this.binhPhuongVaNhan(this.alpha,this.a, this.p)
      do{
        this.k = Math.floor(Math.random(0,1)*999997 + 1)

      }while(this.gcd(this.k,this.p-1) !== 1)
    },
    checkNT(n) {
      for (let i = 2; i <= Math.sqrt(n); i++) {
        if (n % i === 0) {
          return false
        }
      }
      return n > 1
    }
    ,
    randomNT(a, b) {
      let tmp
      do {
        tmp = Math.random(0, 1) * b + a
        tmp = Math.floor(tmp)
      } while (!this.checkNT(tmp))
      return tmp
    }
    ,
    chuyen() {
      this.chuky2 = this.chuky
      this.vanbanky2 = this.vanbanky
      if(this.datatransferinput !== null){
        this.isTxT2 = false
        setTimeout(() => this.test2(this.datatransferinput), 20)
      }
    },
    toBinary(n) {
      let binary = []
      while (n > 0) {
        binary.push(n % 2)
        n = Math.floor(n / 2)
      }
      return binary.reverse()
    }
    ,
    gcd(a, b) {
      while (b !== 0) {
        let tmp = b
        b = a % b
        a = tmp
      }
      return a
    }
    ,
    binhPhuongVaNhan(x, n, m) {
      let binary = this.toBinary(n)
      let p = 1
      let count = 0
      while (count < binary.length) {
        p = p * p
        p = p % m
        if (binary[count] === 1) {
          p = p * x
          p = p % m
        }
        count++;
      }
      return p
    },
    euclidMR(k, p) {

      if (this.gcd(k, p) > 1) {
        return -1
      }
      let r = [p, k]
      let q = [null]
      let s = [1, 0]
      let t = [0, 1]
      let cnt = 0
      while (r[cnt + 1] !== 0) {
        r.push(r[cnt] % r[cnt + 1])
        q.push(Math.floor(r[cnt] / r[cnt + 1]))
        if (cnt > 1) {
          s.push(s[cnt - 2] - q[cnt - 1] * s[cnt - 1])
          t.push(t[cnt - 2] - q[cnt - 1] * t[cnt - 1])
        }
        cnt++
      }
      s.push(s[cnt - 2] - q[cnt - 1] * s[cnt - 1])
      t.push(t[cnt - 2] - q[cnt - 1] * t[cnt - 1])
      if (t[t.length - 1] < 0) {
        t[t.length - 1] = t[t.length - 1] + p
      }
      return t[t.length - 1]


    },
    hamky() {
      let hash = SHA256(this.vanbanky).toString()
      hash = parseInt(hash,16)
      hash = hash%this.p
      this.gamma = this.binhPhuongVaNhan(this.alpha, this.k, this.p)
      let tmp = this.euclidMR(this.k, this.p - 1)
      if (tmp === -1) {
        this.chuky = `Không tồn tồn tại chữ ký với k = ${this.k}`
        return
      }
      let tmp2 = (hash - this.a * this.gamma) % (this.p - 1)
      if (tmp2 < 0) {
        tmp2 = tmp2 + (this.p-1)
      }
      this.delta = (tmp2 * tmp) % (this.p - 1)

      this.chuky = `${this.gamma} ${this.delta}`
    },
    xacnhan() {
      let hash = SHA256(this.vanbanky2).toString()
      hash = parseInt(hash, 16)
      hash = hash % this.p
      let ck = this.chuky2
      let index = null
      for(let i = 0; i < ck.length; i++){
        if(ck[i] === ' '){
          index = i
        }
      }
      let tmp = ck.slice(0,index)
      tmp = parseInt(tmp)
      let tmp2 = ck.slice(index +1 , ck.length+1)
      tmp2 = parseInt(tmp2, 10)
      let vetrai = (this.binhPhuongVaNhan(this.beta, tmp, this.p) * this.binhPhuongVaNhan(tmp, tmp2, this.p)) % this.p
      let vephai = this.binhPhuongVaNhan(this.alpha, hash, this.p)
      if (vetrai === vephai) {
        this.thongbao = 'Chữ ký đúng'
      } else {
        this.thongbao = 'Chữ ký sai'
      }
    }
  }

}

</script>

<style>

#container
section{
  width: 240px !important;
  height: 128px !important;
  min-height: 128px !important;
  padding: 0px !important;
}

#containerr
section{
  width: 240px !important;
  height: 128px !important;
  min-height: 128px !important;
  padding: 0px !important;
}

#A{
  width: 240px !important;
  height: 128px !important;
  min-height: 128px !important;
  padding: 0px !important;
}
</style>
