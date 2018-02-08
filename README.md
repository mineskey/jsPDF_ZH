# jsPDF_ZH
## jsPDF的简单使用以及中文编码问题的解决 ##
文中js通过[CDN][1]引入，若是为了加载时间最好下载至本地。

### jsPDF的使用 ###

 1. jsPDF简介
    jsPDF 是一个基于 HTML5 的客户端解决方案，用于在客户端JavaScript中生成PDF的库。

    有着方法简单，易于实现的优点。

 2. 简单使用
    因为为了页面美观，使用bootstrap进行简单设计，所以要先引入bootstrap相关的css和javascript。
    css:
    ```
    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    ```
    javascript:
    ```
    <script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdn.bootcss.com/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://cdn.bootcss.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
    ```
    html片段:
    ```
    <div class="container">
        <div class="row">
            <h2>导出demo</h2>
            <table class="table table-striped" id="myTable">
                <thead>
                <tr>
                    <th scope="col">ID</th> <th scope="col">First</th>
                    <th scope="col">Last</th> <th scope="col">Handle</th>
                </tr>
                </thead>
                <tbody>
                <tr>
                    <th scope="row">1</th> <td>Mark</td>
                    <td>Otto</td> <td>@mdo</td>
                </tr>
                <tr>
                    <th scope="row">2</th> <td>Jacob</td>
                    <td>Thornton</td> <td>@fat</td>
                </tr>
                <tr>
                    <th scope="row">3</th> <td>Larry</td>
                    <td>the Bird</td> <td>@twitter</td>
                </tr>
                </tbody>
            </table>
            <button class="btn btn-primary" onclick="exportPDF()">导出表格</button>
        </div>
    </div>
    ```
    接下来引入jsPDF的相关javascript:
    ```
    <!--PDF插件START-->
    <script src="https://cdn.bootcss.com/jspdf/1.3.5/jspdf.min.js"></script>
    <script src="https://cdn.bootcss.com/jspdf/1.3.5/jspdf.debug.js"></script>
    <!--PDF插件END-->
    ```
    实现方式:
    ```
    function exportPDF() {
        var doc = new jsPDF('p', 'pt');
        //pdf标题设置
        doc.text(20, 20, 'hello world!');

        doc.save('导出.pdf');
    }
    ```
 3. 导出表格
    为了处理相关表格数据，根据jsPDF引入相关javascript:
    ```
    <script src="https://cdn.bootcss.com/jspdf-autotable/3.0.0-alpha.1/jspdf.plugin.autotable.min.js"></script>
    ```
    简单实现
    ```
    <script type="text/javascript">
        var myTable = $("#myTable");
        // 获取title
        var tableThs = myTable.find("thead th");
        //获取每个tr
        var tableTrs = myTable.find("tbody tr");
        var columns = [];
        //处理title数组
        tableThs.each(function () {
            columns.push({title: $(this).text(), key: $(this).text()});
        });
        //处理数据数组
        var data = [];
        tableTrs.each(function () {
            var tds = $(this).children();
            var object = {};
            //生成数据对象
            $.each(columns, function (i, r) {
                var tdTitle = columns[i].key;
                //'object'跟上文对象名称一致，动态件加属性和值
                eval('object.' + tdTitle + '="' + $(tds).eq(i).text() + '"');
            });
            data.push(object);
        });

        function exportPDF() {
            var doc = new jsPDF('p', 'pt');
            doc.text(20, 20, 'hello world!');
            doc.autoTable(columns, data, {});

            doc.save('导出.pdf');
        }
    </script>
    ```
 4. 中文处理
    导出过程中发现中文显示乱码，根据文档发现jsPDF不支持中文，网上资料是使用`html2canvas`方式转换`canva`方式，并不是十分灵活。后根据项目[jsPDF-CustomFonts-support][2]引入中文字体，解决了导出pdf后中文字体显示乱码的问题。
    这个插件并没有CDN，将项目源码下载到本地，dist文件夹下为相关脚本,font文件夹下为相关字体文件。
    ```
    <script src="../js/jspdf.customfonts.debug.js"></script>
    <script src="../js/jspdf.customfonts.min.js"></script>
    <script src="../js/default_vfs.js"></script>
    ```
    脚本实现
    ```
    var doc = new jsPDF('p', 'pt');
    doc.addFont('NotoSansCJKtc-Regular.ttf', 'NotoSansCJKtc', 'normal');
    //pdf标题设置
    doc.setFont('NotoSansCJKtc');
    //pdf标题设置
    doc.text(20, 20, '导出标题');
    doc.autoTable(columns, data, {});

    doc.save('导出.pdf');
    ```
 5. 最终样式
    ![demo](https://raw.githubusercontent.com/yangfan1995/jsPDF-picture/master/demo.png)
    ![result](https://raw.githubusercontent.com/yangfan1995/jsPDF-picture/master/result.png)

  [1]: http://www.bootcdn.cn/
  [2]: https://github.com/sphilee/jsPDF-CustomFonts-support