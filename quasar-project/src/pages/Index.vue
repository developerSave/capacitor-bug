<template>
  <q-page class="flex flex-center">
    <q-page-container> 
      <q-btn align="center" label="Share file" color="green-6" @click="writeRenFiles()"></q-btn>
    </q-page-container>
  </q-page>
</template>

<script>

import axios from 'axios';
import {Capacitor} from "@capacitor/core";
import { Filesystem, Directory, Encoding } from '@capacitor/filesystem';
import write_blob from "capacitor-blob-writer";
import {FileSharer} from '@byteowls/capacitor-filesharer';
import { Share } from '@capacitor/share';


// const writeFileAndroid = async (blob,path) => {
//     let args ={
//       path: path,
//       data: blob,
//       directory: Directory.Documents,
//       encoding: Encoding.UTF8,
//     }
//     await Filesystem.writeFile(args);
//   };

export default {
  name: 'PageIndex',
    data() {
      return {
        ricerca: false,
        query: '',
        searchedQuery: '',
        search: false,
        ask: false,
        currentPage: 1,
        perPage: 25,
        totalRows: 0,
        items: [],
        fields: [],
        searchResults: [],
        pdfViewer: false,
        pdfFileToView: null,
        currentPdfFile: null,
        pdfRelatedDocuments: [],
        lastUploadedFiles: null,
        ricercaNormative: false,
        busy: false,
        limit: 10,
        dialog: false,
        directory: '',
        fast_mode: true,
        recursive: true,
        fileExtension: null,
        
      };
    },
    methods: {
    getDropboxFile(dropboxFileId, dropboxFileIdRootAnnex) {
      const self = this;
      axios({
        url: process.env.VUE_APP_ROOT_API + 'renplus/getDropboxFile/' +
          dropboxFileId +
          '/' +
          (dropboxFileIdRootAnnex != null ? dropboxFileIdRootAnnex : '-'),
        method: 'get',
        showInCatch: 'true',
        headers: {
          Authorization: self.$store.state.token,
        },
      })
      .then(function(response) {
        console.log('response')
        console.log(response)
        let fileName = response.headers['content-disposition'];

        let url = response.config.url;

        console.log('url')
        console.log(url)
        // var blob = new Blob([response.data]);
        
        // console.log('creo cartella')
        // self.mkdir();

        // try {
        //     var file = new File([filename], filename, {type:"pdf"});
        // } catch(e) {
        //     // when File constructor is not supported
        //     file = new Blob([filename], {type:"pdf"});
        //   }
        // console.log('stampo file')
        // console.log(file)

        // var url  = window.URL.createObjectURL(file);


        console.log('pre download')
        self.writeRenFiles(fileName,url);
      })
      .catch(function(error) {
        console.log('error')
        console.log(error)
      });
    },
    async writeRenFiles(url) {
      await Share.share({
        url: 'https://gosafir.com/mag/wp-content/uploads/2019/12/Tolkien-J.-The-lord-of-the-rings-HarperCollins-ebooks-2010.pdf',
        dialogTitle: 'Condividi il file:',
      })
    },
    shareRenFile(fileName) {
        FileSharer.share({
            filename: fileName,
            contentType: "application/pdf",
            // If you want to save base64:
            base64Data: "...",
            // If you want to save a file from a path:
            // path: 'Download/' + fileName,
        }).then(() => {
            // do sth
        }).catch(error => {
            console.error("File sharing failed", error.message);
        });
    },

    async writeRenFile(fileName,blob) {
      console.log('dentro download')
      await Filesystem.writeFile({
        path: 'TigreMobile/' + fileName,
        data: blob,
        directory: Directory.Documents,
        encoding: Encoding.UTF8,
        recursive: false
      });
      console.log('file salvato')
    },

    async mkdir() {
      try {
        let ret = await Filesystem.mkdir({
          path: 'TigreMobile',
          directory: Directory.Documents,
          recursive: false // like mkdir -p
        });
      } catch(e) {
        console.error('Unable to make directory', e);
      }
    },
    // write_blob(fileName,blob){
    //   console.log('write')
    // path: "Android/Download/" + fileName;
    // directory: Directory.Data;
    // blob: blob;
    // fast_mode: true;
    // recursive: true;
    // console.log('write fine')
    // },

    // writeFileAndroid(blob,fileName){
    //   console.log('writeFileAndroid')
    //   Filesystem.writeFile({
    //     path: "Download/" + fileName,
    //     data: blob,
    //     directory: Directory.Documents, 
    //     encoding: Encoding.UTF8
    //   });
    // },



    // write_blob(blob,fileName){
    //   console.log('dentro write blob')
    //   this.path = "media/renplus/" + fileName;
    //   console.log(this.path)
    //   this.directory= Directory.Data;
    //   console.log( this.directory)
    //   this.fast_mode;
    //   this.blob = blob;
    //   console.log(this.blob)
    //   this.recursive= true;
    // },

    // write_blob(blob,fileName){
    //   Filesystem.writeFile({
    //     path: "media/renplus/" + fileName,
    //     data: blob,
    //     directory: Directory.Data,
    //     encoding: Encoding.UTF8,
    //   });
    // },
  },
};
</script>
<style scoped>
.label{
  font-weight: bold !important;
}
.q-dialog__backdrop
  {
    backdrop-filter: blur(7px) !important;
  }
.download{
  margin-left: auto; 
  margin-right: 0;
}
.close{
  margin-left: 0; 
  margin-right: auto;
}
.namesSpan{
  font-weight: bold !important;
  display: inline-block !important;
}
.div{
  width: 100% !important;
  display: inline-block !important;
}
.dialogRen{
  width: 100% !important;
  height: 100% !important;
}
</style>
