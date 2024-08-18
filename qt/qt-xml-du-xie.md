# QT XML读写

在Qt中，你可以使用QXmlStreamReader 和 QXmlStreamWriter来进行XML的读写操作。还可以使用DOM（文档对象模型）方式读取和写入XML数据。Qt提供了QDomDocument类来表示XML文档，以及相关的类来操作XML元素。

DOM（document object model) 和 Streaming（或称为Pull Parsing）是两种不同的XML解析方式，它们有一些区别。



DOM方式（Document Object Model）：

内存占用：DOM方式将整个XML文档加载到内存中，并构建一个树形结构表示整个文档。这意味着对于大型XML文件，DOM可能占用较多的内存。

随机访问：由于整个文档在内存中，可以轻松实现对文档的随机访问，修改和查询。

适用场景：适用于文档结构较小，需要在解析后频繁随机访问和修改文档的情况。



Streaming方式（Pull Parsing)：

内存占用：Streaming方式以流的形式逐行读取和解析XML文档，不需要将整个文档加载到内存中。这使得它对于大型XML文件更为使用，因为它在解析时只需要保持较小的缓冲区。

逐行处理：以事件驱动的方式逐行处理XML文档，只有在需要时解析下一个元素。这使得Streaming方式更适合处理大型XML文件，而不需要一次性加载整个文档。

适用场景：适用于文档结构较大，但一次只需要处理一部分数据的情况，例如网络流或大型日志文件。





在实践中，选择DOM还是Streaming方式取决于应用程序的需求和数据的大小。如果你需要对整个文档进行频繁的随机访问和修改，DOM方式可能更适合。如果你处理大型XML文件或只需要逐行处理数据，Streaming方式可能更有效。在某些情况下，也可以考虑使用Hybird模式，根据实际需求在两者之间进行选择。



注意事项：

(1) Dom方式读写xml文件，需要加入xml模块，QT += xml



xml读写：

```cpp
#include <QCoreApplication>
#include <QDebug>

#include <QFile>
#include <QXmlStreamWriter>
#include <QXmlStreamReader>

#include <QDomDocument>
// #include <QDomElement>   // 必须.pro文件中 QT += xml
// #include <QDomText>

void writeXmlStream()
{
    QFile file("test_stream.xml");
    if (!file.open(QIODevice::WriteOnly | QIODevice::Text)) {
        qDebug() << "could not open file for writing.";
        return;
    }

    QXmlStreamWriter xmlWriter(&file);

    xmlWriter.setAutoFormatting(true);           // 格式化输出XML
    xmlWriter.writeStartDocument();              // 写入XML文档的开始
    xmlWriter.writeStartElement("root");         // 写入根元素root

    xmlWriter.writeStartElement("person");       //写入子元素person
    xmlWriter.writeAttribute("id", "1");         //写入元素属性
    xmlWriter.writeTextElement("name", "chenchao");   //写入文本元素
    xmlWriter.writeTextElement("age", "36");
    xmlWriter.writeEndElement();                //关闭子元素person

    xmlWriter.writeStartElement("person");       //写入子元素person
    xmlWriter.writeAttribute("id", "2");         //写入元素属性
    xmlWriter.writeTextElement("name", "liuwei");   //写入文本元素person
    xmlWriter.writeTextElement("age", "36");
    xmlWriter.writeEndElement();                //关闭子元素

    xmlWriter.writeStartElement("person");       //写入子元素person
    xmlWriter.writeAttribute("id", "3");         //写入元素属性
    xmlWriter.writeTextElement("name", "liqiang");   //写入文本元素person
    xmlWriter.writeTextElement("age", "36");
    xmlWriter.writeEndElement();                //关闭子元素

    xmlWriter.writeStartElement("person");       //写入子元素person
    xmlWriter.writeAttribute("id", "4");         //写入元素属性
    xmlWriter.writeTextElement("name", "wanggang");   //写入文本元素
    xmlWriter.writeTextElement("age", "36");
    xmlWriter.writeEndElement();                //关闭子元素person

    xmlWriter.writeEndElement();                //关闭根元素root
    xmlWriter.writeEndDocument();               //写入XML文档的结束

    file.close();
}

void readXmlStream()
{
    QFile file("test_stream.xml");
    if (!file.open(QIODevice::ReadOnly | QIODevice::Text)) {
        qDebug() << "could not open file for reading";
    }

    QXmlStreamReader xmlReader(&file);

    while(!xmlReader.atEnd() && !xmlReader.hasError()) {
        QXmlStreamReader::TokenType token = xmlReader.readNext();

        if (token == QXmlStreamReader::StartDocument) {
            continue;
        }

        if (token == QXmlStreamReader::StartElement) {
            if (xmlReader.name() == "root") {
                continue;
            }

            if (xmlReader.name() == "person") {
                qDebug() << "Person ID:" << xmlReader.attributes().value("id").toString();
            }
            if (xmlReader.name() == "name") {
                qDebug() << "Name:" << xmlReader.readElementText();
            }
            if (xmlReader.name() == "age") {
                qDebug() << "Age:" << xmlReader.readElementText();
            }
        }
    }

    if (xmlReader.hasError()) {
        qDebug() << "XML Error:" << xmlReader.errorString();
    }

    file.close();
}

void writeXmlDom()
{
    QDomDocument doc;
    QDomElement root = doc.createElement("root");
    doc.appendChild(root);

    QDomElement person = doc.createElement("person");
    person.setAttribute("id", "1");
    root.appendChild(person);

    QDomElement name = doc.createElement("name");
    QDomText nameText = doc.createTextNode("chenchao");
    name.appendChild(nameText);
    person.appendChild(name);

    QDomElement age = doc.createElement("age");
    QDomText ageText = doc.createTextNode("36");
    age.appendChild(ageText);
    person.appendChild(age);

    QDomElement person1 = doc.createElement("person");
    person1.setAttribute("id", "2");
    root.appendChild(person1);

    QDomElement name1 = doc.createElement("name");
    QDomText name1Text = doc.createTextNode("liuwei");
    name1.appendChild(name1Text);
    person1.appendChild(name1);

    QDomElement age1 = doc.createElement("age");
    QDomText age1Text = doc.createTextNode("36");
    age1.appendChild(age1Text);
    person1.appendChild(age1);


    QDomElement person2 = doc.createElement("person");
    person2.setAttribute("id", "3");
    root.appendChild(person2);

    QDomElement name2 = doc.createElement("name");
    QDomText name2Text = doc.createTextNode("liqiang");
    name2.appendChild(name2Text);
    person2.appendChild(name2);

    QDomElement age2 = doc.createElement("age");
    QDomText age2Text = doc.createTextNode("36");
    age2.appendChild(age2Text);
    person2.appendChild(age2);



    QDomElement person3 = doc.createElement("person");
    person3.setAttribute("id", "4");
    root.appendChild(person3);

    QDomElement name3 = doc.createElement("name");
    QDomText name3Text = doc.createTextNode("wanggang");
    name3.appendChild(name3Text);
    person3.appendChild(name3);

    QDomElement age3 = doc.createElement("age");
    QDomText age3Text = doc.createTextNode("36");
    age3.appendChild(age3Text);
    person3.appendChild(age3);

    QFile file("test_dom.xml");
    if (!file.open(QIODevice::WriteOnly | QIODevice::Text)) {
        qDebug() << "could not open file for writing";
        return;
    }

    QTextStream out(&file);
    doc.save(out, 4);
    file.close();

}

void readXmlDom()
{
    QDomDocument doc;
    QFile file("test_dom.xml");
    if (!file.open(QIODevice::ReadOnly | QIODevice::Text)) {
        qDebug() << "could not open file for reading";
        return;
    }

    if (!doc.setContent(&file)) {
        file.close();
        qDebug() << "failed to load XML content";
        return;
    }

    file.close();

    QDomElement root = doc.documentElement();
    QDomNodeList persons = root.elementsByTagName("person");

    for (int i = 0; i < persons.size(); i ++) {
        QDomElement person = persons.at(i).toElement();
        qDebug() << "Person ID:" << person.attribute("id");

        QDomNodeList children = person.childNodes();
        for (int j = 0; j < children.size(); ++j) {
            QDomNode child = children.at(j);
            if (child.isElement()) {
                QDomElement element = child.toElement();
                qDebug() << element.tagName() << ":" << element.text();
            }
        }
    }
}

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    // Stream写入XML
    writeXmlStream();

    // Stream读取XML
    readXmlStream();

    qDebug() << "==============================";
    // Dom写入XML
    writeXmlDom();

    // Dom读入XML
    readXmlDom();

    return a.exec();
}

```
