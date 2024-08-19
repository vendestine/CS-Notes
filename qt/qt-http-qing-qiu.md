# QT Http请求

在Qt中进行HTTP请求通常可以使用QNetworkAccessManager类。这个类提供了网络操作的功能，包括发送HTTP请求和处理响应。服务器后台会提供一些API。

Qt也提供了QNetworkRequest和QNetworkReply类，它们可以用于更详细的请求和响应处理。以上只是一个简单的例子，实际情况可能需要更多的配置和错误处理。

HTTP（Hypertext Transfer Protocol）是一种用于传输超文本的协议，是web的基础。在HTTP中，GET和POST是两种常见的请求方法，用于从服务器获取资源（GET）或将数据发送到服务器（POST）。

GET请求：用于从服务器获取资源，不会改变服务器的状态

GET请求通常用于请求页面，图片，文本文件等，它是一种幂等的操作，即多次重复的请求对服务器状态没有影响。

POST请求：用于向服务器提交数据，可能会改变服务器的状态。

POST请求通常用于提交表单，上传文件，进行登录等，它不是幂等的，即多次重复的请求可能会对服务器状态产生影响。





注意事项：

(1) QT += network 记得加上，否则会报错undefined reference to \`_imp_\_ ZNK13 QNetworkReply 5errorEv'



QT http请求样例

```cpp
#include <QCoreApplication>
#include <QtNetwork/QNetworkAccessManager>
#include <QtNetwork/QNetworkReply>
#include <QtNetwork/QNetworkRequest>
#include <QObject>
#include <QUrl>
#include <QJsonObject>
#include <QJsonDocument>


void performHttpGet(QNetworkAccessManager &manager, const QUrl& url) {
    // 创建HTTP请求对象
    QNetworkRequest request(url);

    // 发送 GET 请求
    QNetworkReply* reply = manager.get(request);
    QObject::connect(reply, &QNetworkReply::finished, [&](){
        if (reply->error() == QNetworkReply::NoError) {
            QByteArray data = reply->readAll();
            qDebug() << "GET Response:" << data;
        }
        else {
            qDebug() << "GET Error:" << reply->errorString();
        }
        reply->deleteLater();
        qApp->quit();
    });
}



void performHttpPost(QNetworkAccessManager &manager, const QUrl& url) {
    // 创建HTTP请求对象
    QNetworkRequest request(url);

    // 设置请求头
    request.setHeader(QNetworkRequest::ContentTypeHeader, "application/json");

    // 创建要发送的 JSON 数据
    QJsonObject json;
    json["title"] = "foo";
    json["body"] = "bar";
    json["userId"] = 1;

    // 将 JSON 对象转换为字节数组
    QByteArray jsonData = QJsonDocument(json).toJson();

    // 发送 POST 请求
    QNetworkReply* reply = manager.post(request, jsonData);
    QObject::connect(reply, &QNetworkReply::finished, [&](){
        if (reply->error() == QNetworkReply::NoError) {
            QByteArray data = reply->readAll();
            qDebug() << "POST Response:" << data;
        }
        else {
            qDebug() << "POST Error:" << reply->errorString();
        }
        reply->deleteLater();
        qApp->quit();
    });
}

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    // 创建网络访问管理者
    QNetworkAccessManager manager;

    // 执行http GET请求
    QUrl httpGetUrl("http://www.baidu.com");
    performHttpGet(manager, httpGetUrl);

    // 执行http POST请求
    // QUrl httpPostUrl("http://jsonplaceholder.typicode.com/posts");
    // performHttpPost(manager, httpPostUrl);

    return a.exec();
}

```
