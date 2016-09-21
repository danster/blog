## Build Github Pages By Jekyll
<!--toc-->
### 1. Create a repository
Refer to : [Generate a site for your project quickly](https://pages.github.com/)

- create a new repository named username.github.io, where username is your username (or organization name) on GitHub.
- $ git clone https://github.com/username/username.github.io
- $ cd username.github.io
- $ echo "Hello World" > index.html
- $ git add --all
- $ git commit -m 'first commit'
- $ git push 
- Fire up a browser and go to http://username.github.io. Give it a couple of minutes for your page to show up

### 2. install Ruby and Jekyll
- 所有的HTML/Markdown/Textile文件都可以包含YAML配置，这类文件都会被Jekyll解析
- 安装Rdiscount,用来解析Markdown标记的解析包。如果你使用Textile的话，就是安装Kramdow
<br/>
- $ sudo apt-get install ruby1.9.1-dev  
- $ sudo gem install jekyll
- $ sudo gem install rdiscount 

### 3. jekyll serve
- <a href="https://github.com/mojombo/jekyll/wiki">Jekyll</a>
由两个部分协同工作，
一边是将标记语言(Textile, [Markdown](http://daringfireball.net/projects/markdown/syntax))转换为 html 语言的转换器，
另一边是模板引擎 <a href="https://github.com/shopify/liquid/wiki">Liquid</a>
- $ cd username.github.io
- $ jekyll server [--watch] [--drafts] 
- Fire up a browser and go to [http://loaclhost:4000](http://loaclhost:4000)
 
### 参考及相关资料
- [写作环境搭建(git+github+markdown+jekyll) ](http://site.douban.com/196781/widget/notes/12161495/note/264946576/)  
- [使用Jekyll搭建GithubPages博客](http://hzmook.github.io/2012/07/01/use-jekyll-build-blog-on-github.html)
