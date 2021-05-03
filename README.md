# mini-readability


# **Задача**
Имеется необходимость вытаскивать полезный контент из веб документов (без реклам и меню навигации) и представлять его в виде текстового файла для чтения. Форматирование текстовых документов имеет определенные правила и может настраиваться в отдельном файле конфигурации. Результирующий файл необходимо сохранять на диске в определенной директории относительно текущего расположения программы в поддиректориях. Имена поддиректорий должны соответствовать сегментам урла заданного веб документа.

# **Решение**
Приложение выполнено в виде утилиты, для запуска из командной строки. Все подключаемые модули имеются в составе системных библиотек python. Для запуска приложения необходима версия python 3.8.
Основной инструмент для это класс HTMLParser входящий в состав модуля html.parser, который осуществляет обработку документа и инициирует вызов определенных методов в момент
прохождения через различные этапы обработки документа (например: обработка открывающего тега).
Для реализации задачи был создан класс ExtractorContent, наследник класса HTMLParser, в котором перекрыто поведение методов handle_starttag (обработка открывающего тега), handle_data (обработка текста внутри тега), handle_endtag (обработка закрывающего тега). Эти методы вызываются в процессе обработки документа и осуществляют запись полезного контента по абзацам. Полезный контент определяется согласно установленным тегам которые его могут содержать, в большинстве случаев это тег \<p> и текст содержащийся внутри блока \<p> \</p> по умолчанию считается полезным.
Так же по задаче требовалось сохранить ссылки встречающиеся в полезной информации, поэтому особому вниманию требовалась обработка открывающих тегов и их атрибутов. Полезная информация в документе может быть разбита на несколько частей (несколько тегов \<p>) и быть расположена в разных частях документа, но как правило ее обрамляют тегом \<div> со специфическим классом. Учитывая все это приложению необходимо задать основной тег в котором хранится блок с полезной информацией и теги с абзацами в которых эта информация размещена, а так же необходимо перечислить ряд вложенных тегов который могут содержать уточняющую информацию (ссылки).
Все основные настройки хранятся в приложении и могут быть дополнены в отдельном необязательном конфигурационном файле config.json (пример рабочего конфигурационного файла прилагается).

# **Описание настроек:**

Список разрешенных вложенных в основной блок контента тегов.
В большинстве случаев используют тег \<a> но перечислены так же и другие для необходимости сохранения информации.

"list_allow_nested_tags": ["p", "a", "b", "i", "u", "span", "strong"]

Основной тег для заголовка.
"title_tag": "h1"

Основной тег для заголовка второго уровня
"title_tag_2": "h2"

Основной тег для заголовка третьего уровня

"title_tag_3": "h3"

Атрибуты для основного тега заголовка.

"title_tag_attributes": {}

Основной тег контента - блок в котором расположены абзацы с полезной информацией, как правило он специфичен для каждого сайта но в большинстве случаев актуален тег <p>.
"content_tag": "p"

Атрибуты для основного тега. Если он имеет специфические классы или ид, то их можно определить для более точной обработки.
"content_tag_attributes": {}

Основной тег параграфа, который разделяет контент на абзацы.
"paragraph_tag": "p"

Максимальное количество символов в строке, при превышении которого текст будет переноситься на следующую строку по словам.
"word_wrap_column": 80

Настройки шаблона для определенных тегов. Имеют смысл для обработки тега <a> в данной задаче но есть потенциал к использованию более широких возможностей оформления конечного результата. Параметр data определяет шаблон для содержимого тега, а все атрибуты тега описываются как есть без префиксов или постфиксов.
"template_tags": {
    "a": {
        #
        "_data_": "%s",
        "href": "[%s]",
    },
    "b": {
        "_data_": "**%s**",
    },
}


Основной класс которые реализует поставленную задачу KeeperContent он осуществляет доступ к классу ExtractorContent, передает ему актуальный набор настроек для указанного сайта и вытаскивает готовую информацию пригодную для сохранения. Наследником этого класса является FileWebContent который осуществляет запись информации в файл по определенному пути (исходя из урла).

# **Результаты**
http://www.vesti.ru/doc.html?id=2699112&cid=9 - основной контент сохранен, имеется немного мусора.

https://tass.ru/mezhdunarodnaya-panorama/11298507 - контент полностью сохранен, имеется немного муссора
в виде тегов статьи.

https://lenta.ru/news/2021/05/03/dnrr/?utm_source=yxnews&utm_medium=desktop - контент полностью сохранен,
без мусора.

https://vz.ru/opinions/2021/5/2/1097530.html - контент сохранен, из-за особенностей структуры документа
передалось излишнее количество ссылок.

https://ria.ru/20210503/ssha-1730970264.html - основной контент сохранен, из-за особенностей
структуры документа имеется большое количество мусора после контентной части.

https://www.gazeta.ru/auto/news/2021/05/03/n_15935570.shtml - основной контент сохранен, из-за особенностей
структуры документа имеется большое количество мусора после контентной части.

https://regnum.ru/news/polit/3258773.html - основной контент сохранен, есть немного мусора

Работа программы проверялась на версии python 3.8 под операционной системой Microsoft Windows 10.

# **Выводы**
В качестве дальнейшего улучшения программы можно создать мини приложение, которое проходило бы по списку новостных сайтов, собирая новости и выводила бы их пользователю.

Основным улучшением программы может послужить использование сторонних специализированных библиотек для обработки html документов, которые имеют решения разного рода проблем, возникающих в процессе обработки не валидных документов.
