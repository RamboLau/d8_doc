缓存tag是一个字符串。缓存tags是以字符串集合进行传递，因此它们的类型提示为string[]。为什么是string[]，因为一个缓存项依赖许多缓存tags。

我们要求，缓存tags必须形如thing:identifier。