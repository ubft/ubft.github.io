---
layout: post
title:  Ruby-Rspec-Watir 测试环境搭建
date:   2014-12-31 17:00:00
categories: Ruby
---

利用Watir快捷进行浏览器操作测试，替代selenium。

###1. 下载ruby 1.9.3

http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-1.9.3-p551.exe?direct


###2. 安装并配置环境变量
PATH=原有内容;%RUBY_HOME%\bin

```
C:\Users\work>ruby -v
ruby 1.9.3p551 (2014-11-13) [i386-mingw32]
```

###3. 安装rspec,watir

```
gem install rspec
gem install watir
```

若遇到问题
http://www.docin.com/p-672759046.html

可能Watir版本不对

```
gem uninstall watir -v 5.0.0
gem install watir -v 3.0.0
gem uninstall commonwatir -v 4.0.0
```

###4. 运行demo
下载demo
https://github.com/nabilzhang/watir_baidu_demo


{% highlight ruby %}
#demo 核心代码
##页面base
class Base_Page
	def initialize
		b = Watir::IE.new
		b.bring_to_front
		b.maximize
		b
	end
	
	#element
	
	###########################################
	
	#function
	def get_browser
		b = Watir::IE.new
		b.bring_to_front
		b.maximize
		b
	end
end

#----------------------------------
##页面操作
class Index_Page < Base_Page
	
	def initialize ()
		@b = super
		@b.goto "http://www.baidu.com"
	end
	
	#element
	def search_input
		@b.text_field(:id => "kw")
	end
	
	def search_button
		@b.button(:id => "su")
	end
	def resultdiv
		@b.div(:id => "content_left")
	end
	def page_title
		@b.title
	end
	
	###########################################
	
	#function
	def add_content content
		search_input.set(content)
	end
	def search
		search_button.click
        sleep 1
	end
	def get_title
		resultdiv.wait_until_present
		page_title.title
        #return Iconv.conv("utf8","gb2312",page_title.title)
	end
    def close
        @b.close
    end
end

#-----------------------------------------------------
##测试用例
describe "www.baidu.com" do
  it "should search for watir" do
	index_page = Index_Page.new
	index_page.add_content "watir"
    index_page.search
    index_page.resultdiv.should exist
    index_page.close
  end
end
{% endhighlight %}

解压执行
```
rspec starter.rb
```

执行结果
就会自动的打开浏览器，自动搜索Watir，并在终端显示Case是否成功
![Alt text](https://camo.githubusercontent.com/443f4ea026fd072f26143b7764b87cd7b8b19a2f/687474703a2f2f7777322e73696e61696d672e636e2f6c617267652f303035797969354a6a7731656e7370336c363471386a333068703039787461612e6a7067)


###附：参考书籍

- Cucumber & Cheese by Jeff Morgan (Minimum and suggested price: $14.99)
- Watirways by Justin Ko (Minimum price: $0.00, Suggested price: $4.99)
- Web Application Testing in Ruby by ?eljko Filipin (Minimum price: $0.00, Suggested price: $9.99)