# QT JSON读写

在Qt中，你可以使用QJsonObject，QJsonArray，QJsonDocument，QByteArray来读写JSON数据



QJsonDocument:

QJsonDocument类表示一个JSON文档，可以包含一个或多个JSON对象或数组。

它提供了将JSON数据序列化为字符串（toJson()) 以及从字符串解析为JSON数据（from Json())的功能。

toJson()函数返回JSON文档的字节数组表示，你可以通过toJSON(QJsonDocument::Indented)传递QJsonDocument::Indented选项格式化输出。

fromJson()函数用于从包含JSON数据的字节数组构建QJsonDocument。



QJsonObject:

QJsonObject类表示一个JSON对象，即一个键值对的集合。

它提供了添加，查询，删除键值对的方法，可以通过键访问值。

键和值的类型可以是QString，bool，double，QJsonObject，QJsonArray等。

你可以使用insert(), value(), remove()等函数操作QJsonObject中的键值对。



json读写转化流程：

写json：QJsonObject/QJsonArray -> QJsonDocument -> QByteArray -> file

读json: file->QByteArray->QJsonDocument->QJsonObject/QJsonArray



Json读写代码：

```cpp
#include <QCoreApplication>
#include <QJsonArray>
#include <QJsonObject>
#include <QJsonDocument>
#include <QFile>
#include <QDebug>

void writeJson()
{
    QJsonArray jsonArray;

    // 创建第一个JSON对象
    QJsonObject person1;
    person1["name"] = "chen chao";
    person1["age"] = 36;
    person1["city"] = "shen zhen";
    jsonArray.append(person1);

    // 创建第二个JSON对象
    QJsonObject person2;
    person2["name"] = "qu yang";
    person2["age"] = 32;
    person2["city"] = "beijing";
    jsonArray.append(person2);

    // 创建第三个JSON对象
    QJsonObject person3;
    person1["name"] = "liu jun";
    person1["age"] = 31;
    person1["city"] = "wuhan";
    jsonArray.append(person1);

    // 创建json文档
    QJsonDocument jsonDoc(jsonArray);

    // 将json文档保存到文件
    QFile file("test.json");
    if (file.open(QIODevice::WriteOnly | QIODevice::Text)) {
        QTextStream out(&file);
        out << jsonDoc.toJson(QJsonDocument::Indented);
        file.close();
    }


}


void readJson()
{
    QFile file("test.json");
    if (!file.open(QIODevice::ReadOnly | QIODevice::Text)) {
        qDebug() << "could not open file for reading";
        return;
    }

    // 从文件读取json数据
    QByteArray jsonData = file.readAll();

    // 解析Json数据为json文档
    QJsonDocument jsonDoc = QJsonDocument::fromJson(jsonData);
    if (jsonDoc.isNull() || jsonDoc.isEmpty() || !jsonDoc.isArray()) {
        qDebug() << "Invalid Json document.";
        return;
    }

    // 获取json数组
    QJsonArray jsonArray = jsonDoc.array();

    for (const QJsonValue& value: jsonArray) {
        if (value.isObject()) {
            QJsonObject person = value.toObject();
            qDebug() << "name:" << person["name"].toString();
            qDebug() <<"age:" << person["age"].toInt();
            qDebug() <<"City" << person["city"].toString();
        }
    }

    file.close();
}

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    writeJson();
    readJson();

    return a.exec();
}

```
