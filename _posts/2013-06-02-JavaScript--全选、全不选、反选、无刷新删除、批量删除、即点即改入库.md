---
layout: post
title:  JavaScript--全选、全不选、反选、无刷新删除、批量删除、即点即改入库  
categories: JavaScript
description: 全选、全不选、反选、无刷新删除、批量删除、即点即改入库（在yii框架中操作）
keywords: JavaScript, YII
---

```html
<?php    
    header("content-type:text/html;charset=utf-8");    
    use yii\helpers\Html;    
    use yii\widgets\LinkPager;    
    //print_r($countries);die;    
    ?>    
    <h1>显示出数据</h1>    
    <input type="checkbox" value="全选" onclick="check(this);">全选    
    <input type="checkbox" value="全不选" onclick="check_bx(this);">全不选    
    <input type="checkbox" value="反选" onclick="check_fx();">反选    
    <input type="checkbox" value="批量删除" onclick="check_del();">批量删除    
        
    <script src="public/jq.js"></script>    
    <style media="screen">    
      tr{    
        background-color: red;    
        font-family: 宋体;    
        width: 100px;    
        height: 30px;    
        line-height: 30px;    
        text-align: center;    
      }    
      td{    
        background-color: pink;    
        border: 2px solid purple;    
      }    
      .aa{    
        border: 2px solid green;    
        background-color: yellow;    
        font-family: 隶书;    
      }    
      h1{    
        font-family: 华文行楷;    
        box-shadow: 10px 10px 5px #888888;      
        border:2px solid;      
        border-radius:25px;     
        width: 200px;     
        background-color: white;    
      }    
      th{    
        font-family: 隶书;    
        border: 2px solid green;    
        background-color: yellow;    
      }    
      .checkbox{    
        width: 25px;    
        height: 30px;    
      }    
    </style>    
    <table border="1">    
        <th></th>    
        <th>序列号</th>    
        <th>父级ID</th>    
        <th>地区名称</th>    
        <th>操作</th>    
        <?php foreach ($countries as $k => $v) {  ?>    
            <tr>    
              <td><input type="checkbox" id="<?php echo $v['r_id'] ?>" class="checkbox" name="check[]" value="<?php echo $v['r_id'] ?>"></td>    
              <td><?php echo $v['r_id'] ?></td>    
              <td><?php echo $v['pid'] ?></td>    
              <td><span class="num" id="<?php echo $v['r_id'] ?>"><?php echo $v['r_name']?></span></td>    
              <td><a href="javascript:void(0)" id="<?php echo $v['r_id'] ?>" class="aa" onclick="del(this)">删除 </a></td>    
            </tr>    
       <?php } ?>    
    </table>    
    <?= LinkPager::widget(['pagination' => $pagination]) ?>    
    <script type="text/javascript">    
      //即点即改入库    
      $(function(){    
        $(document).on('click','.num',function(){    
            var id=$(this).attr('id');    
            var _this=$(this);    
            var new_val=$(this).html();    
            _this.parent().html("<input type='text'class='asdf' value="+new_val+" id="+id+">");    
            var inp=$('.asdf');    
            inp.focus();    
            inp.blur(function(){    
                var old_id=$(this).attr('id');    
                var old_val=$(this).val();    
                //inp.parent().html("<span class=\"num\" id="+old_id+">"+old_val+"</span>");    
                $.get("index.php?r=upload/updates",{measure_unit:old_val,id:old_id},function(e){    
                    if(e==1){    
                        inp.parent().html("<span class=\"num\" id="+old_id+">"+old_val+"</span>");    
                    }else{    
                        inp.parent().html("<span class=\"num\" id="+old_id+">"+new_val+"</span>");    
                    }    
                })    
            })    
        })    
      })    
    </script>    
    <script>    
        //无刷新删除    
          function del(obj)    
        {    
            var ids=obj.id;    
            ajax=new XMLHttpRequest();    
            ajax.onreadystatechange=function()    
            {    
                if(ajax.readyState==4)    
                {    
                    //alert(ajax.responseText);    
                    if(ajax.responseText==1)    
                    {    
                        obj.parentNode.parentNode.parentNode.removeChild(obj.parentNode.parentNode);    
                    }    
                    else{    
                        alert("删除失败");    
                    }    
                }    
            }    
            ajax.open("get","index.php?r=upload/del&ids="+ids);    
            ajax.send(null);    
        }    
        /*全选*/    
            function check(obj)    
        {    
            var ids=document.getElementsByName("check[]");    
            if(obj.checked)    
            {    
                for(var i=0;i<ids.length;i++)    
                {    
                    ids[i].checked=true;    
                }    
            }    
        }    
        /*全不选*/    
         function check_bx(obj)    
        {    
            var ids=document.getElementsByName("check[]");    
            if(obj.checked)    
            {    
                for(var i=0;i<ids.length;i++)    
                {    
                    ids[i].checked=false;    
                }    
            }    
        }    
          //反选    
        function check_fx()    
        {    
            var ids=document.getElementsByName("check[]");    
            for(var i=0;i<ids.length;i++)    
            {    
                ids[i].checked=!ids[i].checked;    
            }    
        }    
        /*批量删除*/    
         function check_del()    
        {    
            var ids=document.getElementsByName("check[]");    
            var str='';    
            for(var i=0;i<ids.length;i++)    
            {    
                if(ids[i].checked)    
                {    
                    str=str+','+ids[i].value;    
                }    
            }    
            new_str=str.substr(1);    
            ajax=new XMLHttpRequest();    
            ajax.onreadystatechange=function() {    
                if (ajax.readyState == 4) {    
                    //alert(ajax.responseText);      
                    if(ajax.responseText==1)    
                    {    
                        for(var j=ids.length-1;j>=0;j--)    
                        {    
                            if(ids[j].checked)    
                            {    
                              ids[j].parentNode.parentNode.parentNode.removeChild(ids[j].parentNode.parentNode);    
                            }    
                        }    
        
                    }    
                    else    
                    {    
                        alert("删除失败");    
                    }    
                }    
            }    
            ajax.open("get","index.php?r=upload/del_all&new_str="+new_str);    
            ajax.send(null);    
        }    
    </script>    
```
```php
    <?php    
    namespace app\controllers;    
        
    use Yii;    
    use yii\filters\AccessControl;    
    use yii\web\Controller;    
    use yii\filters\VerbFilter;    
    use app\models\LoginForm;    
    use app\models\ContactForm;    
    use yii\web\UploadedFile;    
    use app\models\Upload;    
    use yii\data\Pagination;    
    use app\models\Country;    
    use app\models\Region;    
        
    class UploadController extends Controller    
    {    
        //public $layout=false; //禁用yii自带样式    
        public function actionIndex(){    
            $model = new Upload();    
            if ($model->load(Yii::$app->request->post()) && $model->validate()) {    
                // 验证 $model 收到的数据    
                // 做些有意义的事 ...    
                return $this->render('entry-confirm', ['model' => $model]);    
            } else {    
                // 无论是初始化显示还是数据验证错误    
                return $this->render('entry', ['model' => $model]);    
            }    
        }    
        public function actionAdds()    
        {    
            $model = new Upload();    
            $request = Yii::$app->request;    
            $post=$request->post('Upload');    
            $u_name = $post['u_name'];    
            $u_pwd = $post['u_pwd'];    
            //在浏览器输出的值是 yii\web\UploadedFile Object ( [name] => 2.jpg [tempName] => C:\Windows\php3986.tmp    
            // [type] => image/jpeg [size] => 216848 [error] => 0 )    
             $arr =  $model->u_img = UploadedFile::getInstance($model,'u_img');    
             //print_r($arr);    
              if ($model->upload()){    
                    $u_img = $arr->name;    
                    //var_dump($u_img);    
                     $connection = \Yii::$app->db;    
                        $result=$connection->createCommand()->insert('upload', [    
                    'u_name' => $u_name,    
                    'u_pwd' => $u_pwd,    
                     'u_img' =>$u_img,    
                ])->execute();    
                if($result)    
                {    
                  echo "添加成功";    
                }    
                else    
                {    
                    echo "添加失败";    
                }    
                }    
        
        }    
        /*分页*/    
        public function actionLists()    
         {    
             $query = Country::find();    
        
             $pagination = new Pagination([    
                 'defaultPageSize' => 1,    
                 'totalCount' => $query->count(),    
             ]);    
        
             $countries = $query->orderBy('name')    
                 ->offset($pagination->offset)    
                 ->limit($pagination->limit)    
                 ->all();    
        
             return $this->render('lists', [    
                 'countries' => $countries,    
                 'pagination' => $pagination,    
             ]);    
         }    
             /*地区表进行分页*/    
             public function actionShow(){    
                 $query = Region::find();    
                 $pagination = new Pagination([    
                         'defaultPageSize' => 6,    
                         'totalCount' => $query->count(),    
                 ]);    
                 $countries = $query->orderBy('r_id')    
                         ->offset($pagination->offset)    
                         ->limit($pagination->limit)    
                         ->all();    
                 return $this->render('show', [    
                         'countries' => $countries,    
                         'pagination' => $pagination,    
                 ]);    
             }    
             /*修改*/    
             public function actionUpdates(){    
                 $name = $_GET['measure_unit'];    
                $id = $_GET['id'];    
                 $connection = \Yii::$app->db;    
                 $command = $connection->createCommand("UPDATE region SET r_name='$name' WHERE r_id='$id'");    
                 $command->execute();    
                 if(!empty($command)){    
                     echo 1;    
                 }else{    
                     echo 0;    
                 }    
             }    
             // 无刷新delete    
             public function actionDel(){    
                $id=$_GET['ids'];    
                $connection=\Yii::$app->db;    
                $arr=$connection->createCommand("delete from region where r_id='$id'")->execute();    
                if($arr){    
                    echo 1;    
                }else{    
                    echo 0;    
                }    
             }    
             /*   
            批量删除   
            @new_str  GET   
            */    
             public function actionDel_all(){    
                $new_str=$_GET['new_str'];    
                $connection=\Yii::$app->db;    
                $arr=$connection->createCommand("delete from region where r_id in($new_str)")->execute();    
                if($arr){    
                    echo 1;    
                }else{    
                    echo 0;    
                }    
             }    
    }    
    ?>    
```
