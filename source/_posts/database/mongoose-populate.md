---
title: Mongoose Populate 基本使用
tags: 'Mongodb'
categories: 'Databse'
top_img: '../../img/database.jpg'
---
> 在使用 mongoose 时，通过一个外键与另一张表建立关联

<a href="https://mongoosejs.com/docs/index.html" target="_blank">mongoose官网地址</a>

## <a href="https://blog.csdn.net/Elliott_Yoho/article/details/53537147" target="_blank">Mongoose 之 Population 使用</a>

语法
```
Query.populate(path, [select], [model], [match], [options])
```
* path  ： String或Object。
   - String类型的时， 指定要填充的关联字段，要填充多个关联字段可以以空格分隔。
   - Object类型的时，就是把 populate 的参数封装到一个对象里。当然也可以是个数组。下面的例子中将会实现。

* select：Object或String，可选，指定填充 document 中的哪些字段。
   - Object类型的时，格式如: {name: 1, _id: 0}，为0表示不填充，为1时表示填充。
   - String类型的时，格式如: “name -_id”，用空格分隔字段，在字段名前加上-表示不填充。详细语法介绍 query-select

尝试中发现 select 默认会填充 _id。

* model: Model，可选，指定关联字段的 model，如果没有指定就会使用Schema的ref。

* match: Object，可选，指定附加的查询条件。

* options：Object，可选，指定附加的其他查询选项，如排序以及条数限制等等。

## 基本使用 

数据模型
创建三个Schema和Model。
```javascript
var mongoose = require('mongoose');
mongoose.Promise = require('bluebird');
mongoose.connect('mongodb://localhost/population');

var Schema = mongoose.Schema;
var userSchema = new Schema({
    name: String,
    age: Number,
    posts: [{
        type: Schema.Types.ObjectId, 
        ref: 'post'
    }],
    comments: [{
        type: Schema.Types.ObjectId, 
        ref: 'comment'
    }]
});
var User = mongoose.model('user', userSchema);

var postSchema = new Schema({
    title: String,
    content: String,
    author: {
        type: Schema.Types.ObjectId, 
        ref: 'user'
    },
    comments: [{
        type: Schema.Types.ObjectId, 
        ref: 'comment'
    }]
});
var Post = mongoose.model('post', postSchema);

var commentSchema = new Schema({
        content: String,
        author: {
            type: Schema.Types.ObjectId, 
            ref: 'user'
        }
})
var Comment = mongoose.model('comment', commentSchema);

exports.User = User;
exports.Post = Post;
exports.Comment = Comment;
```
在上述的例子中，创建了三个 Models:User，Post，Comment。

User 的属性 posts，对应是一个 ObjectId 的数组。ref表示关联Post(注意: 被关联的model的 type 必须是 ObjectId, Number, String, 和 Buffer 才有效)。

Post的属性 poster 和 comments 分别关联User和Comment。

Comment的属性 post 和 commenter 分别关联Post和User。

三个 Models 的关系:一个 user--has many--&gt;post。一个 post--has one--&gt;user，has many--&gt;comment。一个 comment--has one--&gt;post 和 user。
> 注: ref 对应是在connection中注册过的model。
```javascript
var User = mongoose.model('user', userSchema);
...
author: {type: Schema.Types.ObjectId, ref: 'user'}
// 这里的 ref: 'user' 是第一行的 mongoose.model('user', userSchema) 第一个参数。
```


填充User的posts字段:
```javascript
//填充所有 users 的 posts
User.find()
    .populate('posts', 'title', null, {sort: { title: -1 }})
    .exec(function(err, docs) {
            onsole.log(docs[0].posts[0].title); // post-by-aikin
    });

//填充 user 'luajin'的 posts
User.findOne({name: 'luajin'})
    .populate({path: 'posts', select: { title: 1 }, options: {sort: { title: -1 }}})
    .exec(function(err, doc) {
        console.log(doc.posts[0].title);  // post-by-luajin
    });

//这里的 populate 方法传入的参数形式不同，其实实现的功能是一样的，只是表示形式不一样。

//填充所有 users 的 posts
User.find()
    .populate('posts', 'title', null, {sort: { title: -1 }})
    .exec(function(err, docs) {
        console.log(docs[0].posts[0].title); // post-by-aikin
    });

//填充 user 'luajin'的 posts
User.findOne({name: 'luajin'})
    .populate({path: 'posts', select: { title: 1 }, options: {sort: { title: -1 }}})
    .exec(function(err, doc) {
        console.log(doc.posts[0].title);  // post-by-luajin
    });
//这里的 populate 方法传入的参数形式不同，其实实现的功能是一样的，只是表示形式不一样。</pre>
```
## 填充多个字段
假如，我们要填充 Post 中的 author 和 comments，且填充 author 的 name 和 age，还有 comments 的 content； 不填充 author 和 comments 的 _id。
```javascript
// 若是用字符串填写，select 同时作用于两个字段，即 author 和 comments 都会填充 name age content，若该字段没有这些数据，则不填充。

Post.findOne({
    'title': 'test'
}).populate('author comments', 'name age content -_id').exec()
.then(function(post) {
    console.log(post);
}).catch(function(reason) {
    console.log(reason);
});

// 数组形式可以单独对某一字段用 select 选择要填充的数据。
Post.findOne({
    'title': 'test'
}).populate([
    {
    path: 'author', select: 'name age -_id'
    }, 
    {
    path: 'comments', select: 'content -_id'
}]).exec()
.then(function(post) {
      console.log(post);
}).catch(function(reason) {
      console.log(reason);
});
// 结果：
{ 
    _id: 584a030733604a156a4f6600,
    author: { name: 'Tom', age: 19 },
    title: 'test',
    content: 'wakaka',
    __v: 1,
    comments: [ { content: 'walala' } ] 
}

```

