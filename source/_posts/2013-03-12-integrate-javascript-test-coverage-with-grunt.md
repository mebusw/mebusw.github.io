title: javascript 单元测试覆盖率工具，及Grunt集成
tags:
  - grunt
  - js
categories:
  - programming
  - javascript
---

js单元测试Unit Test的工具包括Jasmine, Mocha, NodeUnit, JsTestDriver等。

以[Jasmine](http://blog.csdn.net/mebusw/article/details/pivotal.github.com/jasmine/)最为流行，因为其良好的语义和丰富的Mocking机制。

无论使用[Grunt.js](http://blog.csdn.net/mebusw/article/details/gruntjs.com)或是[Maven](http://blog.csdn.net/mebusw/article/details/maven.apache.org)来作为项目构建工具，Jasmine都有良好的插件支持。

js单元测试其实并不为前端开发者重视，近年开始node.js以及单页面web app流行起来后，越来越多的js项目也开始采用了单元测试，至于单元测试覆盖率，对于很多js开发者还很新鲜。

笔者所在项目采用grunt.js作为项目工具。之前使用了grunt.js v0.3,采用grunt-jasmine-runner插件来运行测试，但一直苦于没有合适的覆盖率插件，后来发现了grunt-istanbul，然而两者不能够直接集成，需要对源代码进行改动，以使运行测试的浏览器（比如phantomjs）将生成的覆盖率数据，从内存中传递到grunt的上下文中。

于是，笔者在github上fork了项目，自行将两者整合成为完整的一步式插件[grunt-jasmine-coverage](https://github.com/mebusw/grunt-jasmine-coverage)，并发布到NPM上。

近期，grunt升级到v0.4, 官方的jasmine插件也换成了[grunt-contrib-jasmine](https://github.com/gruntjs/grunt-contrib-jasmine)。经过笔者一番探索和讨论，发现该插件支持模板系统用于扩展功能，而且已有热心人制作了用于覆盖率的模板[grunt-template-jasmine-istanbul](https://github.com/mebusw/grunt-template-jasmine-istanbul)。经过与其作者的讨论，现在该模板已经可以很好的支持同时生成多份覆盖率报告。比如，cobertura格式的报告，可以用于[Jenkins](http://jenkins-ci.org/)集成，而lcov格式的报告，可以用于Sonar集成(需要[sonar-javascript插件](http://blog.csdn.net/mebusw/article/details/docs.codehaus.org/display/SONAR/JavaScript+Plugin))

有一个小技巧，项目中除了包括自己的源代码，肯定也会包含其他第三方的库，比如jquery。而对于计算覆盖率，我们可能希望将第三方代码踢除出去。这时，在grunt-contrib-jamine的配置中，记得将第三方代码置入`vender`一节中，这样[grunt-template-jasmine-istanbul](https://github.com/mebusw/grunt-template-jasmine-istanbul)在做instrument时会忽略掉这些代码。

此外，grunt.js对于nodeunit也有很好的支持