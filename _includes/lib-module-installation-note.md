{% assign url_segments = page.url | split: "/" %}
{% assign lib_idx = url_segments | size | minus: 1 %}
{% assign lib_name = url_segments[lib_idx] %}
## Installation
Follow generic instruction for `c64lib` 
[installation]({{ site.baseurl }}/pages/install) 
in order to bring "{{ page.title }}" into your Kick Assembler project. Simply
clone the repository into directory of choice:

    git clone https://github.com/c64lib/{{ lib_name }}.git

Remember to add a directory that contains cloned {{ lib_name }} repository
to your `libdir` when executing Kick Assembler.
