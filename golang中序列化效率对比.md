# gob，protobuf，json在golang中的序列化效率对比

2018年02月09日 15:07:07

阅读数：587

先上代码：

```
looptimes:=10000



	u:=User{66,"nxin","beijing"}



	gobbegintimestamp:=strconv.FormatInt(time.Now().UTC().UnixNano(), 10)



	gobbeginint,_:=strconv.Atoi(gobbegintimestamp)



	fmt.Println("gob序列化==============================",gobbeginint)



	buf := new(bytes.Buffer)   //分配内存



	enc := gob.NewEncoder(buf) //创建基于buf内存的编码器



	for i:=0;i<looptimes;i++ {



 



		err := enc.Encode(u)       //编码器对结构体编码



		if err != nil {



			log.Fatal(err)



		}



	}



	gobendtimestamp:=strconv.FormatInt(time.Now().UTC().UnixNano(), 10)



	gobendint,_:=strconv.Atoi(gobendtimestamp)



	fmt.Println("===================END===================",gobendint)



 



	jsonbegintimestamp:=strconv.FormatInt(time.Now().UTC().UnixNano(), 10)



	jsonbeginint,_:=strconv.Atoi(jsonbegintimestamp)



	fmt.Println("json序列化==============================", jsonbeginint)



	for j:=0;j<looptimes;j++ {



		_, e := json.Marshal(u)



		if e!=nil{



			log.Fatal(e)



		}



	}



	jsonendtimestamp:=strconv.FormatInt(time.Now().UTC().UnixNano(), 10)



	jsonendint,_:=strconv.Atoi(jsonendtimestamp)



	fmt.Println("===================END===================",jsonendint)



 



 



	protobufbegintimestamp:=strconv.FormatInt(time.Now().UTC().UnixNano(), 10)



	protobufbeginint,_:=strconv.Atoi(protobufbegintimestamp)



	fmt.Println("protobuf序列化==============================", protobufbeginint)



	hw:=&model.Helloworld{10,"wang","beijing"}



	for j:=0;j<looptimes;j++ {



		_, e := proto.Marshal(hw)



		if e!=nil{



			log.Fatal(e)



		}



	}



	protobufendtimestamp:=strconv.FormatInt(time.Now().UTC().UnixNano(), 10)



	protobufendint,_:=strconv.Atoi(protobufendtimestamp)



	fmt.Println("===================END===================",protobufendint)



 



 



	fmt.Println("json:",jsonendint-jsonbeginint)



	fmt.Println("gob:",gobendint-gobbeginint)



	fmt.Println("protobuf:",protobufendint-protobufbeginint)
```

尝试了100,1000,10000,100000次的序列化对比时间：

总结：

总体来说protobuf的效率最高，gob的效率比json的还要低。

100次时三者相差不大。

=====================================================================

1000次时三者表现不稳地，测试出来的结果：

![img](https://img-blog.csdn.net/20180209145849792?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![img](https://img-blog.csdn.net/20180209145927154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![img](https://img-blog.csdn.net/2018020915000353?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

以前一种出现的次数更多。

=====================================================================

10000次出现的结果，protobuf效率明显要高，但是json与gob差别不大：

![img](https://img-blog.csdn.net/2018020915011149?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![img](https://img-blog.csdn.net/2018020915012565?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![img](https://img-blog.csdn.net/20180209150138179?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

=====================================================================

100000次出现的结果：

![img](https://img-blog.csdn.net/20180209150300307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![img](https://img-blog.csdn.net/20180209150312641?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![img](https://img-blog.csdn.net/20180209150324297?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29uZ2Nvbnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

protobuf还是明显优势，但是gob有点落后。

综上所述：在数据量小的时候三者差不多，但是数据量大了以后protobuf会更好，但是gob显得力不从心，json表现中庸。