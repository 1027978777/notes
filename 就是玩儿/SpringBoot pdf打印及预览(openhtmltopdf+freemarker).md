# SpringBoot pdf打印及预览(openhtmltopdf+freemarker)

1.  **添加依赖**

[openhtmltopdf](https://github.com/danfickle/openhtmltopdf)+[freemarker](http://freemarker.foofun.cn/)

```xml
  <properties>
        <openhtml.version>1.0.10</openhtml.version>
    </properties>
<!--openhtmltopdf -->
   <dependencies>
        <dependency>
            <!-- ALWAYS required, usually included transitively. -->
            <groupId>com.openhtmltopdf</groupId>
            <artifactId>openhtmltopdf-core</artifactId>
            <version>${openhtml.version}</version>
        </dependency>
        <dependency>
            <!-- Required for PDF output. -->
            <groupId>com.openhtmltopdf</groupId>
            <artifactId>openhtmltopdf-pdfbox</artifactId>
            <version>${openhtml.version}</version>
        </dependency>
        <dependency>
            <!-- Optional, leave out if you do not need logging via slf4j. -->
            <groupId>com.openhtmltopdf</groupId>
            <artifactId>openhtmltopdf-slf4j</artifactId>
            <version>${openhtml.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
  </dependencies>
```

1.  **在/resources/template/view/html新建模板xxx.ftl文件**

```html
<html>
<head>
    <meta charset="UTF-8"/>
    <style>
    /* 特有css样式自行参考官网 */
        @page {
            size: a4;
            @top-left {
                content: element(header-left);
            }
            @top-center {
                content: element(header-center);
            }
            @top-right {
                content: element(header-right);
            }
            @bottom-center {
                font-size: 14px;
                content: counter(page);
                font-family: 'simsun', serif;
            }
            margin: 50px;
        }

        * {
            margin: 0;
            padding: 0;
            font-family: 'simsun', serif;
        }

        #page-header-left {
            font-size: 10px;
            font-weight: bold;
            position: running(header-left);
        }

        #page-header-center {
            font-size: 10px;
            font-weight: bold;
            position: running(header-center);
        }

        #page-header-right {
            font-size: 10px;
            font-weight: bold;
            position: running(header-right);
        }

        table {
            width: 100%;
            font-size: 14px;
            border-collapse: collapse;
            font-family: 'simsun', serif;
            border-spacing: 0;
            /* The magical table pagination property. */
            /*-fs-table-paginate: paginate;*/
            /* Recommended to avoid leaving thead on a page by itself. */
            -fs-page-break-min-height: 0.5cm;
            border-left: 0.07cm solid black;
            border-right: 0.07cm solid black;
            border-bottom: 0.03cm solid black;
        }

        .checkbox {
            display: inline-block;
            position: relative;
            top: 1px;
            width: 10px;
            height: 10px;
            border: 1px solid black;

        }

        .correct {
            display: inline-block;
            position: relative;
            top: 2px;
            right: 15px;
            width: 7px;
            height: 3px;
            border-left: 1px solid black;
            border-bottom: 1px solid black;
            -webkit-transform: rotate(-45deg);
            transform: rotate(-45deg);
            margin-right: -16px;
        }

        input[type = "checkbox"] {
            width: 10px;
            height: 10px;
            border: 1px solid black;
        }

        tr, thead, tfoot {
            page-break-inside: avoid;
        }

        td, th {
            page-break-inside: avoid;
            font-family: 'simsun', serif;
            padding: 1px;
            border-top: 0.03cm solid black;
            border-left: 0.03cm solid black;
        }


        td:first-child, th:first-child {
            border-left: 0;
        }

        #table-title {
            text-align: center;
            margin-top: 0;
            margin-bottom: 5px;
            font-weight: bold;
        }  
       
    </style>
</head>
<body>
<div class="header-box">
    <div id="page-header-left">
        <span id="page-header-text">编号：<span style="font-weight: normal">${code!''}</span></span>
    </div>
    <div id="page-header-center">
    <span id="page-header-text">时间 <sup>1)</sup>：<span
                style="font-weight: normal">${time?date!''}</span></span>
    </div>
</div>
<div class="basic-box">
    <table style="border-top: 0;">
        <tr>
            <td>标题1</td>
            <td>${xx1!''}</td>
            <td>标题2</td>
            <td>${xx2!''}</td>
            <td>标题3</td>
            <td>${xx3!''}</td>
        </tr>
         <tr>
            <td style="font-weight: bold;text-align: left;">结果</td>
            <td colspan="3" style="text-align: left;">
                <div>
                    <div class="checkbox"></div>
                    <#if result??&&result == '1'>
                        <div class="correct"></div>
                    </#if>
                    <div style="display: inline-block">合格</div>
                    <div class="checkbox"></div>
                    <#if result??&&result == '0'>
                        <div class="correct"></div>
                    </#if>
                    <div style="display: inline-block">不合格</div>
                </div>
            </td>
            <td colspan="4" style="font-weight: bold;text-align: left;">签字人：</td>
        </tr>
    </table>
</div>



</body>
</html>
```

1.  **controller接口**

```java
   @RequestMapping(value = "/print/pdf/{code}", produces = MediaType.APPLICATION_PDF_VALUE)
    public ResponseEntity<byte[]> printPdf(@PathVariable("code") String detectCode) throws IOException, TemplateException {
        Assert.hasText(code, "code不能为空");
        return detectReportPrintService.findPdf(code);
    }
```

1.  **service实现类**

> 📌**pdf预览有中文乱码，需要重新引入simsun.ttf字体文件，放在/resources/fonts文件夹下（与代码路径一致即可），ftl文件中css样式必须font-family: 'simsun', serif;**

> 📌在 `Configuration` 中可以使用下面的方法来方便建立三种模板加载。 (每种方法都会在其内部新建一个模板加载器对象，然后创建 `Configuration` 实例来使用它。)
> 1\. void setDirectoryForTemplateLoading(File dir);
> 2\. void setClassForTemplateLoading(Class cl, String prefix);
> 3\. void setServletContextForTemplateLoading(Object servletContext, String path);
> **项目打包为jar包只能使用setClassForTemplateLoading方法。**

```java
    public ResponseEntity<byte[]> findPdf(String code) throws IOException, TemplateException {
        //获取ftl所需变量值的自定义方法
        Map data = this.findValue(code);
        //FreeMarker 生成 html字符串
        Configuration conf = new Configuration(Configuration.VERSION_2_3_23);
        conf.setDefaultEncoding("UTF-8");
        //加载模板文件(模板的路径)
        //有多种加载模板的方法
        //项目打包为jar包只能使用setClassForTemplateLoading方法
        //第一个参数：springboot启动类，第二个参数：ftl模板对应resources下的位置
        conf.setClassForTemplateLoading(XaWebApplication.class, "/template/view/html");
        // 加载模板
        Template template = conf.getTemplate("/xxx.ftl");
        //传入变量生成html字符串
        String htmlString = FreeMarkerTemplateUtils.processTemplateIntoString(template, data );
        //openhtmltopdf生成pdf
        @Cleanup ByteArrayOutputStream os = new ByteArrayOutputStream();
        PdfRendererBuilder builder = new PdfRendererBuilder();
        builder.withHtmlContent(htmlString, "");
        builder.useFont(new FSSupplier<InputStream>() {
            @SneakyThrows
            @Override
            public InputStream supply() {
                return resourceLoader.getResource("classpath:fonts/simsun.ttf").getInputStream();
            }
        }, "simsun");
        builder.toStream(os);
        builder.run();
        byte[] bytes = os.toByteArray();
        return new ResponseEntity<>(bytes, HttpStatus.OK);
    }
```
