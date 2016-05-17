title: Test of Nodejs
date: 2016-05-17 21:11:00
tags:
- test
- node
- ava
- travis ci
- benchmark
- nyc

# test of nodejs

该文[Node.js 测试总结](http://pinggod.com/2016/Test/)对几种测试做了介绍，简单实用。摘录之。


## 单元测试

单元测试，又称模块测试，针对程序中的最小执行单元进行正确性测试。常见的开发模式包括 TDD 和 BDD 两类。

TDD（Test-driven development，测试驱动开发），先编写测试用例，然后针对测试用例开发模块，当测试用例不足时，补充测试用例；当模块无法通过测试时，持续更新模块代码，直到完全通过测试用例。其开发核心围绕测试用例展开，即测试用例的完整性决定了开发模块的健壮性和正确性，这容易由边界条件引发单元测试覆盖度不够的问题。

BDD（Behavior-driven development，行为驱动开发），用语义化的编程语言开发紧贴业务需求的测试用例，继而驱动相关模块的开发。

[AVA](https://github.com/sindresorhus/ava) 是 JavaScript 生态中最新潮的测试框架，其内置了 Babel，可以直接使用 ES6 语法，具有轻量高效、并发执行、强制隔离等优点，安装方法：

	npm install --save-dev ava
	
设置 `package.json` 中的 `scripts` 字段：

	{
	    "scripts": {
	        "test": "ava",
	        "test:watch": "ava --watch"
	    }
	}	

运行：

	npm test
	# or
	npm test:watch

例子：

	import test from 'ava';
	
	const fibonacci = (n) => {
	    if (n === 0 || n === 1) {
	        return n;
	    }
	    return fibonacci(n - 1) + fibonacci(n - 2);
	}
	
	test('Test Fibonacci(0)', t => {
	    t.is(fibonacci(0), 0);
	});
	
	test('Test Fibonacci(1)', t => {
	    t.is(fibonacci(1), 1);
	});
	
	// HOOK CALLS
	test.before('Before', t => {
	    console.log('before');
	});
	
	test.after('After', t => {
	    console.log('after');
	});
	
	test.beforeEach('BeforeEach', t => {
	    console.log('   beforeEach');
	});
	
	test.afterEach('AfterEach', t => {
	    console.log('   afterEach');
	});

AVA 提供了一下修饰方法来指定测试的执行方式：

* skip()，跳过添加了 skip() 的测试用例
* only()，只执行添加了 only() 的测试用例
* todo()，占位标识符，表示将来需要添加的测试用例
* serial()，串行执行测试用例，默认情况下 AVA 会以并行的方式执行测试用例

代码中`t`，称为断言执行对象，该对象包含以下方法：

* `t.end()`，结束测试，只在 test.cb() 中有效
* `t.plan(count)`，指定执行次数
* `t.pass([message])`，测试通过
* `t.fail([message])`，测试失败
* `t.ok(value, [message])`，断言 `value` 的值为真值
* `t.notOK(value, [message])`，断言 `value` 的值为假值
* `t.true(value, [message])`，断言 `value` 的值为 true
* `t.false(value, [message])`，断言 `value` 的值为 `false`
* `t.is(value, expected, [message])`，断言 `value === expected`
* `t.not(value, expected, [message])`，断言 `value !== expected`
* `t.same(value, expected, [message])`，断言 `value` 和 `expected` 深度相等
* `t.notSame(value, expected, [message])`，断言 `value` 和 `expected` 深度不等
* `t.throws(function | promise, [error, [message]])`，断言 `function` 抛出异常或 `promise` reject 错误
* `t.notThrows(function | promise, [message])`，断言 `function` 不会异常或 `promise` resolve
* `t.regex(contents, regex, [message])`，断言 `contents` 匹配 `regex`
* `t.ifError(error, [message])`，断言 `error` 是假值

## 集成测试

相对于专注微观模块的单元测试，集成测试是从宏观整体的角度发现问题，所以也称为组装测试和联合测试。[Travis CI](https://travis-ci.org/) 是一款优秀的持续集成工具，可以监听 Github 项目的更新，便于开源软件的集成测试。使用 Travis CI 需要在项目的根目录下创建 `.travis.yml` 配置文件（以 Node.js 为例）：

	language: node_js
	
	node_js:
	    - "6"
	    - "5"
	
	before_script:
	
	script:
	    - npm test
	    - node benchmark/index.js
	
	after_script:

默认情况下，`Travis CI` 会自动安装依赖并执行 `npm test` 命令，通过 `script` 字段可以自定义需要执行的命令，其完整的生命周期包括：

* Install apt addons
* before_install
* install
* before_script
* script
* after_success or after_failure
* OPTIONAL before_deploy
* OPTIONAL deploy
* OPTIONAL after_deploy
* after_script


## 基准测试

基准测试使用严谨的测试方法、测试工具或测试系统评估目标模块的性能，常用于观测软硬件环境发生变化后的性能表现，其结果具有可复现性。在 Node.js 环境中最常用的基准测试工具是 [Benchmark.js](https://benchmarkjs.com/docs)，安装方式：

	npm install --save-dev benchmark

基本示例：

	const Benchmark = require('benchmark');
	const suite = new Benchmark.Suite;
	
	suite.add('RegExp#test', function() {
	    /o/.test('Hello World!');
	})
	.add('String#indexOf', function() {
	    'Hello World!'.indexOf('o') > -1;
	})
	.on('cycle', function(event) {
	    console.log(String(event.target));
	})
	.on('complete', function() {
	    console.log('Fastest is ' + this.filter('fastest').map('name'));
	})
	// run async
	.run({ 'async': true });

## 代码覆盖率

代码覆盖率工具根据测试用例覆盖的代码行数和分支数来判断模块的完整性。AVA 推荐使用 `nyc` 测试代码覆盖率，安装 nyc：

	npm install nyc --save-dev

修改 `.gitignore` 忽略相关文件：

	node_modules
	coverage
	.nyc_output
	
修改 `package.json` 中的 `test` 字段：

	{
	    "scripts": {
	        "test": "nyc ava"
	    }
	}

执行 `npm test`，得到代码覆盖率结果。




## 参考

* [Node.js 测试总结](http://pinggod.com/2016/Test/)