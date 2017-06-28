# Roate-Image
图片旋转方案以及代码实现


1.先将图片文件读出来
2.获取图片本地路径
3.加载本地图片，试用canvas旋转图片
4.重新绘制图片，并转换成base64
5.根据需要将base64转换成blob对象


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>

    <style>
        img{
            /*width: 400px;*/
            /*height: 200px;*/
        }
    </style>
</head>

<body>
<input type="file" onchange="change()" class="photoUpLoad" multiple="" accept="image/*">
<img id="img" src="">

<canvas id="can"></canvas>



<script src="https://cdn.bootcss.com/zepto/1.0rc1/zepto.min.js"></script>
<!--<script src='https://github.com/think2011/localResizeIMG/blob/master/dist/lrz.bundle.js'></script>-->

<script>

//    参考
//    https://github.com/think2011/localResizeIMG
//    http://www.jianshu.com/p/ad4501db178e
//    http://www.blogjava.net/jidebingfeng/articles/406171.html


/**
 * 旋转图片，重新提交文件
 */
function change(){

    //获取图片文件
    var imgFile = $(".photoUpLoad")[0].files[0];
    //获取图片本地url
    var url = getObjectURL(imgFile);

    //测试用，展示图片
    $("#img").attr("src",url);//没用，展示图片旋转

    //旋转图片
    rotateImage(url, 270);
    

    //该插件可以获取图片的原始信息
    //如需使用，需要引入：https://github.com/think2011/localResizeIMG/blob/master/dist/lrz.bundle.js

//    EXIF.getData(imgFile, function () { // IMG_FILE为图像数据
//        var orientation = EXIF.getTag(this, "Orientation");
//
//
//        // 调整为正确方向
//        switch (orientation) {
//            case 3://180°
//                rotateImage(url, 180);
//                break;
//            case 6://顺时针90°
//                rotateImage(url, 90);
//                break;
//            case 8://逆时针90°
//                rotateImage(url, 270);
//                break;
//            case 2:
//                break;
//            case 4:
//                break;
//            case 5:
//                break;
//            case 7:
//                break;
//
//            default:
//        }
//
//    });

}



/**
 * 获取图片文件URL/blob
 */
function getObjectURL(file) {
    var url = null;
    if (window.createObjectURL != undefined) {
        url = window.createObjectURL(file)
    } else if (window.URL != undefined) {
        url = window.URL.createObjectURL(file)
    } else if (window.webkitURL != undefined) {
        url = window.webkitURL.createObjectURL(file)
    }
    return url
}



/**
 * 旋转图片
 * 仅限于选装0、90、180、270、360度
 */
function rotateImage(img,angle) {
    var canvas = document.getElementById('can');
    var ctx = canvas.getContext('2d');

    var image = new Image();

    image.onload = function() {
        var w = image.width,
            h = image.height;

        var halfw = w/2,
            halfH = h/2;


        if(angle == 90 || angle == 270){
            canvas.width = h;
            canvas.height = w;

            //旋转画布，绘制图片
            ctx.save();//保存状态
            ctx.translate(halfH,halfw);//设置画布上的(0,0)位置，也就是旋转的中心点
            ctx.rotate(angle*Math.PI/180);

            ctx.drawImage(image,-halfw,-halfH,w,h);//把图片绘制在旋转的中心点，

            ctx.restore();//恢复状态
        }
        else{
            canvas.width = w;
            canvas.height = h;

            //旋转画布，绘制图片
            ctx.save();//保存状态
            ctx.translate(halfw,halfH);//设置画布上的(0,0)位置，也就是旋转的中心点
            ctx.rotate(angle*Math.PI/180);
            ctx.drawImage(image,-halfw,-halfH);//把图片绘制在旋转的中心点，
            ctx.restore();//恢复状态
        }

        

        //将canvas转存为base64
        var imageBase64 = canvas.toDataURL("image/png");

        //将base64转换为blob对象
        var blobImg = convertBase64UrlToBlob(imageBase64);

        console.log(blobImg);

    };

    image.src = img;
}


/**
 * 将以base64的图片url数据转换为Blob
 * @param urlData
 * 用url方式表示的base64图片数据
 */
function convertBase64UrlToBlob(urlData){

    var bytes = window.atob(urlData.split(',')[1]);        //去掉url的头，并转换为byte

    //处理异常,将ascii码小于0的转换为大于0
    var ab = new ArrayBuffer(bytes.length);
    var ia = new Uint8Array(ab);
    for (var i = 0; i < bytes.length; i++) {
        ia[i] = bytes.charCodeAt(i);
    }

    return new Blob( [ab] , {type : 'image/png'});
}

</script>
</body>
</html>
