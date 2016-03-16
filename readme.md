This is maxking's(Abhilash Raj) blog.

This blog uses jekyll to generate the website and thus requires ruby and jekyll
to be installed on the system before running this. The rakefile has been adopted
from [here][1].

The following commands are handy to manage the website:

```
rake build                                     # Build for deployment (but do not deploy)
rake check_links                               # Check links for site already running on localhost:4000
rake clean                                     # Clean up generated site
rake create_post[date,title,category,content]  # Create a post
rake deploy                                    # Build and deploy to remote server via rsync
rake deploy_github                             # Build and deploy to Github
rake list_changes                              # Print the file changed since last deploy
rake post_changes                              # Create a post listing all changes since last deploy
rake preview                                   # Preview on local machine (server with --auto)
```


New posts are added in _drafts.

[1]: https://github.com/avillafiorita/jekyll-rakefile
