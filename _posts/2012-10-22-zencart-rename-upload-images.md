---
layout: post
title: "重命名 zencart 上传的图片"
author: Infong
category: spl
tags: [zencart, php]
---

在新版的 zencart 中，上传的图片不会被命名成其它文件，从而导致后台上传的图片都是原始的文件名称，一来不安全，二来经常会跟现有的图片冲突，三来又会有一些带有 urlcode 的文件名不能正常访问。

所以就想对它的文件名进行重命名：

1. 修改 `ADMIN/includes/classes/upload.php` 类。在第约 72 行：

        - $this->set_filename($file['name']); 
        + $this->set_filename(md5(time()).'.'.end(explode('.',$file['name'])));

2. 如果想连上传目录也一起改掉的话，可以在 `ADMIN/includes/modules/new_product_preview.php` 的第 15 行插入：

        // 上传目录的变更
        if(empty($_POST['img_dir'])) {
          $_POST['img_dir'] = date('Ym') . '/';
        }
        if(!is_dir(DIR_FS_CATALOG_IMAGES . $_POST['img_dir'])) {
          mkdir(DIR_FS_CATALOG_IMAGES . $_POST['img_dir']);
        }

3. 如果有用到 Image_Handler 的话，也要进行相应的更改，约 90 行：

        if(empty($_POST['imgBaseDir'])) {
          $_POST['imgBaseDir'] = date('Ym') . '/';
        }
        
这样，上传图片的时候，就会按像 `images/201210/64c15fdbab0ccaa5f79875381ffccf86.jpg` 形式进行重命名了。
