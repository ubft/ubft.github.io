---
layout: post
title:  Ruby-Rspec-Watir ���Ի����
date:   2014-12-31 17:00:00
categories: Ruby
---

###1. ����ruby 1.9.1

http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-1.9.3-p551.exe?direct


###2. ��װ�����û�������
PATH=ԭ������;%RUBY_HOME%\bin

```
C:\Users\work>ruby -v
ruby 1.9.1p0 (2009-01-30 revision 21907) [i386-mswin32]
```

###3. ��װrspec,watir

```
gem install rspec
gem install watir
```

����������
http://www.docin.com/p-672759046.html

����Watir�汾����
```
gem uninstall watir -v 5.0.0
gem install watir -v 3.0.0
gem uninstall commonwatir -v 4.0.0

```

###4. ����demo
����demo
https://github.com/nabilzhang/watir_baidu_demo

```
#demo ���Ĵ���
##ҳ��base
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

#--------------------------------------------------------
##ҳ�����
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
##��������
describe "www.baidu.com" do
  it "should search for watir" do
	index_page = Index_Page.new
	index_page.add_content "watir"
    index_page.search
    index_page.resultdiv.should exist
    index_page.close
  end
end
```

��ѹִ��
```
rspec starter.rb
```

ִ�н��
![Alt text](./BaiduHi_2014-12-31_11-12-18.png)


###�����ο��鼮

- Cucumber & Cheese by Jeff Morgan (Minimum and suggested price: $14.99)
- Watirways by Justin Ko (Minimum price: $0.00, Suggested price: $4.99)
- Web Application Testing in Ruby by ?eljko Filipin (Minimum price: $0.00, Suggested price: $9.99)