# TbsForOffice
腾讯TBS浏览服务应用内打开Office（Word，PPT，PDF）文件

一般可有三种方案:
    
* 由后台开发人员把Office文件转换成Html文件
* 调用手机的第三方浏览器打开
* 利用X5浏览器在应用内打开

## 文件下载(OKhttp)
```
 private  void   downLoadFile(){
        Observable.create(new ObservableOnSubscribe<FileVo>() {
          @Override
            public void subscribe(final ObservableEmitter<FileVo> e) throws Exception {
              final FileVo  fileVo=new FileVo();
               String path= FileUtil.getCachePath(TbsReaderActivity.this);
                downloadUtil.download(officeUrl, path,
                        officeSaveName,
                        new DownloadUtil.OnDownloadListener() {
                            @Override
                            public void onDownloadSuccess(File file) {
                                fileVo.setFile(file);
                                e.onNext(fileVo);
                                e.onComplete();
                            }
                            @Override
                            public void onDownloading(int progress) {
                                showProgress(progress);
                            }
                            @Override
                            public void onDownloadFailed(Exception e) {
                            }
                        });
            }

        }).compose(RxUtils.schedulersTransformer()).subscribe(new Consumer<FileVo>() {
            @Override
            public void accept(FileVo fileVo) {
                 showOffice(fileVo);
            }
        });

    }
```

### 文件加载
```
 private   void   showOffice(FileVo fileVo){
         progressBar.setProgress(fileVo.getProgress());
         file=fileVo.getFile();
         String bsReaderTemp = tbsReaderTemp;
         File bsReaderTempFile =new File(bsReaderTemp);
         if (!bsReaderTempFile.exists()) {
             boolean mkdir = bsReaderTempFile.mkdir();
             if(!mkdir){
                 Log.d("print","创建/TbsReaderTemp失败！！！！！");
             }
         }
         //加载文件
         Bundle localBundle = new Bundle();
         localBundle.putString("filePath", file.toString());
         localBundle.putString("tempPath",
                 tbsReaderTemp);
         if (tbsReaderView == null){
             tbsReaderView = getTbsView();
         }
         boolean result = tbsReaderView.preOpen(FileUtil.getFileType(file.toString()), false);
         if (result) {
             tbsReaderView.openFile(localBundle);
         }
     }

```
   ###添加视频播放，使用pdfview查看pdf文件
   ### PS:如果本文对你有帮助请点个star或者Fork，如有问题可在Issues中进行讨论
    
    
