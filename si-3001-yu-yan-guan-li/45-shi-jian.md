我们从 https://localize.drupal.org/download 下载的一般是后缀为po的文件。
基本格式说明：

.po文件都是由一对对的msgid和msgstr组成的。
msgid就是原文(Message ID)。
msgstr就是译文（Message Translation)。
原文、译文相互对照，所以非常适于翻译。
基本示例
示例--这是未翻译的.po文件：
 msgid "Welcome to ADempiere ERP"
 msgstr ""
示例--这是翻译后的.po文件：
 msgid "Welcome to ADempiere ERP"
 msgstr "欢迎来到ADempiere ERP"