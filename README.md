# Serial-Load-Image
串行请求图片


由于页面使用滚动加载图片加上移动端网络带宽较小，页面中的商品描述处又都是图片展示。
从而导致页面出现较长时间的空白，即使加上了loading的图片，也未必可以让用户接受长时间的等待。
综合分析了一下页面图片加载时间长的原因，除了图片大小以外，就是并发请求导致的问题。
因为滚动加载，用户往往一次拉页面拉到底部，从而使得所有的图片请求都发起了，此时，对于PC端来说，并发是非常高效的。
但是，在移动端则不然，因为带宽小而导致并发请求都堵塞在一起，进而导致第一张图片的延时加长。
因此，我通过以下核心方法使得图片的请求以串行的方式发起，进而有效的减小了第一张图片的延时。
详细的比较可以参见demo代码。

 var descImg=[],isNoLoad=true;
 var addList=function(em,url){
      var item=descImg.length;
      descImg[item]={};
      descImg[item].dom=em;
      descImg[item].src=url;
      if(isNoLoad){
         isNoLoad=false;
         serial_load(item);    
      }
 };
 var serial_load=function(i){
      var item=descImg[i],img=item.img,em=item.dom;
      img=new Image();
      img.src=item.src;
      img.onabort=img.onerror=img.onload = function() {
          em.attr("src", img.src);
          em.removeAttr('init_src');
          if(i==descImg.length-1){
              isNoLoad=true;
          }else{
             serial_load(i+1);
          }
      };
 };
