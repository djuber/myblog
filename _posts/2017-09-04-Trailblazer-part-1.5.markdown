---
layout: post
date: 2017-09-04 21:39 -0500
title: Trailblazer part 1.5
categories: 
---
Still spending a little time working along the traiblazer guides. I ran into the weird situation where the example code unsatisfactorily allows malformed page rendering (I'm holding my breath in hopes that this improves, in the meantime I modified my approach to use a traditional rails view, so instead of this in the guide's example index method:
```
  render cell(BlogPost::Cell::Index, result["model"]), layout: false
```

I did something more like this:
```
  @html = cell(BlogPost::Cell::Index, result["model"])
```

And in the view (now a regular index.html.erb file including the application layout via normal methods, I needed to include `<%= raw @html.show %>`. I'm not sure whether well formatted pages matters yet, my feeling is that these should be components rather than pages, and the "raw widget" view is a preview. As I said, I'm looking forward to more details as I wrap up the guide and start diving further into the available documentation.