#Build Podcast Ep 20 - Nanoc
[Screencast link ](http://build-podcast.com/nanoc/)

[Nanoc](http://nanoc.stoneship.org/) is tool for building static web sites. Using a static site generator like Nanoc has some advantages such as speed, security and local development. In this episode, we will explore how to build a blog with Nanoc!

#Background on Nanoc:

1. [Nanoc documentation](http://nanoc.stoneship.org/docs/1-introduction/) and [api 3.4](http://nanoc.stoneship.org/docs/api/3.4/Nanoc.html)
2. [Nanoc code repo](https://github.com/ddfreyne/nanoc)
3. [Build a blog with Nanoc](http://www.danhoey.com/blog/2011_09_23_building_a_blog_with_nanoc/)
4. [Rocking and rolling with Nanoc](http://zameermanji.com/blog/2012/6/15/rocking-and-rolling-with-nanoc/)
5. [build a static site with nanoc](http://clarkdave.net/2012/02/building-a-static-blog-with-nanoc/)
6. [Using nanoc for blogging](http://logs.drbig.one.pl/articles/using_nanoc_for_blogging.html)
7. [Learn to code the Hard Way in Ruby](http://ruby.learncodethehardway.org/book/) by Zed Shaw

#Things to learn with Nanoc


###1. check pre-requisites:

In the command line::

1. **ruby** programming language is installed: `ruby -v`
2. **rubygem** is installed: `gem -v`
3. install **nanoc**: `gem install nanoc` 
    - check nanoc is installed `nanoc --version`
4. install **kramdown**, ruby implementation of [Markdown](http://daringfireball.net/projects/markdown/): `gem install kramdown`
    - check [kramdown](http://kramdown.rubyforge.org/) is installed: `kramdown --version`
5. install **adsf** to preview our site in any directory with a web server: `gem install adsf`
    - check [adsf](http://stoneship.org/software/adsf/) is installed in all the list of gems: `gem list`

###2. create a static site with nanoc

in the command line:

1. create new blog folders and files: `nanoc create_site blog && cd blog`
2. view all contents inside the folder: `tree`
3. create all the view files: `nanoc compile`
    - if you get [an error](https://github.com/ddfreyne/nanoc/issues/69) `RuntimeError: Could not read content/index.html because the file is not valid ASCII-8BIT.`, then add the following in config.yml on line 53 after `layouts_root: /`:
    
        ```
        config:
          encoding: utf-8
        ```
4. `nanoc view` and view the site [http://localhost:3000/](http://localhost:3000/)

###3. amend content and create new pages

1. amend **content** on file `content/index.html` and run `nanoc compile && nanoc view`
2. amend **metadata**, title on file `content/index.html` line 2 and `nanoc compile && nanoc view`
3. use **Markdown** to write by adding the following in the file `Rules` on line 20 before the line `compile '*' do`:
    
    ```
    compile '*' do
      if item.binary?
        # don’t filter binary items
      else
        filter :kramdown
        layout 'default'
      end
    end
    ```
4. to create **partials**:
    - include `include Nanoc::Helpers::Rendering` in file `lib/default.rb`
    - create the partial file e.g. `defaults/sidebar.html`
    - `<%= render 'sidebar' %>` in the `layouts/defaults.html` at the line where sidebar will appear
5. to **automate** nanoc compilation, view in browser and refresh browser page, run `nanoc autocompile` and start the [chrome](https://www.google.com/intl/en/chrome/browser/) extension [live reload](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)

###4. create blog posts

1. include in `lib/default.rb`

    ```
    include Nanoc3::Helpers::LinkTo
    include Nanoc3::Helpers::Blogging
    ```
1. create 4 articles in `content/*.html` with the following format:

    
    ```
    ---
    kind: article
    title: Hello World
    created_at: 1 September 2012
    ---
    
    contents in markdown

    ```
3. include the post title and date in `layouts/default.html`

    ```
    <h1><%= @item[:title] %></h1>
    <p class="created_at"><%= @item[:created_at] %></p>
    <%= yield %>
    ```
2. list the posts in chronological order in `layouts/default.html` on home page

    ```
    <% if (@item[:title] == 'Welcome') %>
    <ul>
        <% (@site.sorted_articles[0..30]).each do |post| %>
        <li>
        <%= link_to(post[:title], post.path) %>
        <%= post[:created_at] %>
        </li>
        <% end %>
    </ul>
    <% end %>
    ```
3. include next/previous post links:
    - define the next and previous links in `lib/default.rb`
    
    ```
        def previous_link
    	prv = sorted_articles.index(@item) - 1
    	if sorted_articles[prv].nil?
    		return ""
    	else
    		link_to('← Previous', sorted_articles[prv].reps[0])
    	end
    end
    
    def next_link
    	nxt = sorted_articles.index(@item) + 1
    	if sorted_articles[nxt].nil?
    		return ""
    	else
    	link_to('Next &rarr;', sorted_articles[nxt].reps.find { |r| r.name == :default })
    	end
    end
    ```
    - put the links on `layout/default.html` if it's an article:
    
    ```
    <% if (@item[:kind] == 'article') %>
        <p>it's an article!!!</p>
        <p><%= previous_link %> | <%= next_link %></p>
    <% end %>
    ```

###5. create feed

1. create `content/feed.erb`:
    
    ```
    <%= atom_feed :title => 'My Blog', :author_name => 'Sayanee', :author_uri => 'http://localhost:3000', :limit => 10 %>
    ```
2. include compilation rule before `compile '*'`:
    
    ```
    compile '/feed/' do
      filter :erb
    end
    ```
3. include routing rule before `route '*'`

    ```
    route 'feed' do
      '/feed.xml'
    end
    ```
4. include base url in `config.yml` in the last line:

    ```
    base_url: 'http://localhost:3000'
    ```

#More Resources on Static Site Generators

1. [List of Static Site Generators](http://nanoc.stoneship.org/docs/1-introduction/#similar-projects)
2. [Jekyll](http://jekyllrb.com/), a ruby static site generator
3. [Punch](http://laktek.github.com/punch/), a node.js static site generator
4. [Benifits of using a static site generator](http://nimbupani.com/redesign-notes.html): Redesign notes by [Divya](http://twitter.com/divya)

#Related Build Podcast episodes

- [Package Managers](http://build-podcast.com/package-managers/) - rubygem
- [Markdown](http://build-podcast.com/markdown/) - how to format text to churn out html
- [Wordpress](http://build-podcast.com/wordpress/) - another blogging platform with GUI and WYSIWYG editor

#Build Link of this Episode

[You and Your Research](http://www.cs.virginia.edu/~robins/YouAndYourResearch.html) by Richard Hamming and a [summary](http://www.gfz-potsdam.de/portal/gfz/Services/PHD+Studenten/Ressourcen/PDFfolder/Hamming+Rules;jsessionid=9AD800FF23C7491A4FC21D3746AA926A?binary=true&status=300&language=en) of his talk.