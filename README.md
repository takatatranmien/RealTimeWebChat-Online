# RealTimeWebChat-Online
Chat thoi gian thuc

Tạo 1 ứng dụng chat trực tuyến đơn giản sử dụng Nodejs, ExpressJs, SocketIO, Jade

Một trong ứng dụng quan trọng của fb chính là message, tức là người dùng có thể chat trực tuyến với nhau trên trang web hay ứng dụng, và gần như người dùng có trải niệm việc chat diễn ra gần như ngay lập tức. Thậm chí có thể tạo 1 nhóm chat với nhau, gửi hình ảnh cũng như có các biểu tượng cảm xúc. Tính năng đó thực sự khá hấp dẫn, vậy làm sao để làm được điều đó, tôi đưa đến cho các bạn 1 kỹ thuật trong số nhiều cách đó là sử dụng NodeJs, ExpressJs, SocketIo để giải quyết vấn đề này. Ngoài ra việc tạo template thì tôi sử dụng Jade (Giờ được đổi tên thành Pug). Trong bài này chỉ là những ứng dụng đơn giản để các bạn có hình dung đầu tiên về việc chat real time.

Cài đặt môi trường
Như tôi đã nói ở đây tôi sử dụng 4 kỹ thuật đó là Node Js, Express Js, Socket Io và Jade vậy ta cần thiết phải cài đặt 4 bộ thư viện này. Đầu tiên cần phải cài NodeJs thì các bạn có thể vào trực tiếp trang chủ hoặc download trực tiếp tại đây. Sau khi cài đặt thì để cài 3 bộ thư viện còn lại có 2 cách là sử dụng trong command line hoặc tạo ra 1 file package.json

Cài đặt bằng command line

Để cài từng thư viện ta dùng các lệnh sau

ExpressJs

npm install express --save
SocketIo

npm install socket.io
Jade template

Hiện giờ Jade được đổi sang Pug các bạn có thể xem trên Github

npm install jade
Cài đặt bằng tạo file package.json

Thay vì việc ta phải chạy 3 lệnh trên và có thể bị nhầm lẫn nên ta có thể tạo 1 file package để cài đặt đồng thời cả 3 thư viện trên. Tạo 1 file package.json ngay tạo root folder của project với nội dung như sau

{
    "name": "RealTimeWebChat",
    "version": "0.0.0",
    "description": "Real time web chat",
    "dependencies": {
        "socket.io": "latest",
        "express": "latest",
        "jade": "latest"
    },
    "author": "developer"
}
Sau khi tạo xong để tải thư viện ta chỉ cần dùng lệnh dưới để tải hết thư viện về.

npm install
Thiết lập chạy Express Js
Sau khi tải các thư viện trên, ta cần chạy node js bằng cách tạo 1 file là index.js với nội dung như sau:

var express = require("express");
var app = express();
var port = 3701;

app.get("/", function(req, res){
    res.send("It works!");
});

app.listen(port);
console.log("Listening on port " + port);
Đoạn code trên là ta thiết lập môi trường chạy Express Js với port là 3701, và để kiểm tra thì đầu tiên ta chạy lệnh:

node index.js
Lệnh trên sẽ luôn sử dụng khi ta thay đổi source tức là khi bạn thay đổi gì đó ta cần dừng lệnh và chạy lại để Express Js sẽ chạy những source mới nhất của ta đã làm.
Khi chạy lệnh đó thì trên màn hình command sẽ hiển thị dòng Listening on port 3701 sau đó mở trình duyệt ra gõ đường dẫn là localhost:3701 thì sẽ hiển thị dòng chữ It works! như hình dưới là chúc mừng bạn đã qua bước thiết lập Express Js

Screenshot from 2016-03-26 13:59:44.png

Tạo giao diện với Jade
Ở đây ta có thể tạo giao diện sử dụng thư viện Jade template. Phần giao diện chúng ta sẽ để ở folder template và ta cần khai báo sử dụng trong file index.js bằng cách thêm dòng sau:

//Template
app.set('views', __dirname + '/template');
app.set('view engine', "jade");
app.engine('jade', require('jade').__express);
app.get("/", function(req, res){
    res.render("index");
});
Ở đây ta cần để ý đoạn res.render("index"); tức là ta sẽ cần tạo 1 file là index.jade trong folder template mà ta vừa tạo. Và file đó có nội dung như sau:

html
    head
        title= "Real time web chat"
    body
        #content(style='width: 500px; height: 300px; margin: 0 0 20px 0; border: solid 1px #999; overflow-y: scroll;')
        .controls
            input#field(style='width:350px;')
            input#send(type='button', value='send')
Và sau khi chạy node index.js rồi mở trình duyệt ta sẽ có kết quả như hình dưới

Screenshot from 2016-03-26 10:47:23.png

Nhìn giao diện ta cũng có thể hiểu là nội dung chat sẽ ở box to ở trên và input ở dưới sẽ gõ nội dung chat và bấm nút send là được. Vậy làm sao để thực hiện được việc gửi message đi thì ta cần đi tiếp bước theo làm việc với Socket Io.

Gửi và hiển thị nội dung chat
Đầu tiên ta cần khai báo sử dụng folder public để chưa các đoạn javascript cho project bằng lệnh app.use(express.static(__dirname + '/public')); và tạo 1 folder là public.
Tiếp đó ta khai báo sử dụng socketio trong file index.js

var io = require('socket.io').listen(app.listen(port));
io.sockets.on('connection', function (socket) {
    socket.emit('message', { message: 'welcome to the chat' });
    socket.on('send', function (data) {
        io.sockets.emit('message', data);
    });
});
Lưu ý ta cần bỏ dòng app.listen(port); đi. Như vậy file index.js của chung ta đầy đủ sẽ là

var express = require('express');
var app = express();
var port = 3701;

//Template
app.set('views', __dirname + '/template');
app.set('view engine', "jade");
app.engine('jade', require('jade').__express);
app.get("/", function(req, res){
    res.render("index");
});

//Socket
app.use(express.static(__dirname + '/public'));
var io = require('socket.io').listen(app.listen(port));
io.sockets.on('connection', function (socket) {
    socket.emit('message', { message: 'welcome to the chat' });
    socket.on('send', function (data) {
        io.sockets.emit('message', data);
    });
});
Đồng thời ta tạo 1 file để xử lý các sự kiện là file chat.js trong folder public với nội dung như sau

window.onload = function() {

    var messages = [];
    var socket = io.connect('http://localhost:3701');
    var field = document.getElementById("field");
    var sendButton = document.getElementById("send");
    var content = document.getElementById("content");

    socket.on('message', function (data) {
        if(data.message) {
            messages.push(data.message);
            var html = '';
            for(var i=0; i<messages.length; i++) {
                html += messages[i] + '<br />';
            }
            content.innerHTML = html;
        } else {
            console.log("There is a problem:", data);
        }
    });

    sendButton.onclick = function() {
        var text = field.value;
        socket.emit('send', { message: text });
    };

}
```Javascript

Sau khi tạo xong ta cần khai báo load 2 file chat.js với socketio trong phần head của file index.jade như sau

```Jade
script(src='/chat.js')
script(src='/socket.io/socket.io.js')
Các bạn không phải lo lắng việc không có file socket.io.js nó sẽ tự tải về.
Sau khi làm xong như việc trên các bạn chạy thử node index.js và trải niệm thôi. Tôi đã chạy thử và kết quả như hình dưới, gần như nhận được luôn.

Screenshot from 2016-03-26 13:23:29.png

Thật không ngờ phải không? Việc tạo ra 1 ứng dụng chat như thế này quá nhẹ nhàng và đơn giản :D

Phát triển thêm
Để tăng thêm phần chức năng, tôi thêm vài tính năng như thêm phần tên người dùng.
Đầu tiên thêm phần text cho user name cho dưới phần .control, bạn nhớ lưu ý có | đằng trước nhé

| Name:
input#name(style='width:350px;')
br
Vậy toàn bộ nội dung file index.jade của chúng ta như sau

html
    head
        title= "Real time web chat"
        script(src='/chat.js')
        script(src='/socket.io/socket.io.js')
    body
        #content(style='width: 500px; height: 300px; margin: 0 0 20px 0; border: solid 1px #999; overflow-y: scroll;')
        .controls
            | Name:
            input#name(style='width:350px;')
            br
            input#field(style='width:350px;')
            input#send(type='button', value='send')
Giờ ta xử lý nhận dữ liệu ở file chat.js và nội dung toàn bộ file như sau:

window.onload = function() {

    var messages = [];
    var socket = io.connect('http://localhost:3701');
    var field = document.getElementById("field");
    var sendButton = document.getElementById("send");
    var content = document.getElementById("content");
    var name = document.getElementById("name");

    socket.on('message', function (data) {
        if(data.message) {
            messages.push(data);
            var html = '';
            for(var i=0; i<messages.length; i++) {
                html += '<b>' + (messages[i].username ? messages[i].username : 'Server') + ': </b>';
                html += messages[i].message + '<br />';
            }
            content.innerHTML = html;
        } else {
            console.log("There is a problem:", data);
        }
    });

    sendButton.onclick = function() {
        if(name.value == "") {
            alert("Please type your name!");
        } else {
            var text = field.value;
            socket.emit('send', { message: text, username: name.value });
        }
    };

}
Giờ chúng ta trải niệm thôi, kết quả như hình sau

Screenshot from 2016-03-26 13:34:26.png

Thật không thể tin nổi :D

Kết luận
Qua đây chúng ta thấy việc tạo ra 1 ứng dụng nhỏ cho việc chat trực tuyến không khó lắm, khá đơn giản phải không? Tuy nhiên trong quá trình làm cũng có vài khó khăn như nếu như trong đoạn code của ta bị phát sinh lỗi thì toàn bộ dự án treo luôn (sad). Và ở đây chưa có phần lưu lại nội dung chat cũ từ quá khứ. Những cái này có thể chúng ta nghiên cứu kỹ hơn khi làm việc với Mongo DB.
