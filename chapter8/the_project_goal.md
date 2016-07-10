# 项目目标

我们将在已有的例子中创建一个新的`Activity`。这个`Activity`将通过StackExchange API从stackoverflow检索出最活跃的10位用户。App使用这些信息来展示一个包含用户头像、姓名、名望数以及住址的列表。对每一位用户，app使用OpenWeatherMap API来检索该用户住址当地的天气预报，并显示一个小天气图标。基于从StackOverflow检索的信息，app对列表中的每一位用户提供一个`onClick`事件，打开他们在个人信息中设定的个人网站或者Stack Overflow的个人主页。