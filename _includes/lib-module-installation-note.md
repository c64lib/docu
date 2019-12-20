{% assign files = page.files | sort %}
{% assign subroutines = page.subroutines | sort %}
{% assign subroutines_count = subroutines | size %}
{% assign sample_file = files | sample %}
{% assign url_segments = page.url | split: "/" %}
{% assign lib_idx = url_segments | size | minus: 2 %}
{% assign lib_name = url_segments[lib_idx] %}
## Installation and usage
Follow generic instruction for `c64lib` 
[installation]({{ site.baseurl }}/pages/install) 
in order to bring "{{ page.title }}" into your Kick Assembler project.
Remember to add a directory that contains cloned {{ page.title }} repository
to your `libdir` when executing Kick Assembler.

The {{ page.title }} module consists of following files:
{% for file in files %}* `{{ file }}`
{% endfor %}

{% if subroutines_count > 0 %}
Additionally, the {{ page.title }} module consists of following subroutines:
{% for sub in subroutines %}* `{{ sub }}`
{% endfor %}
{% endif %}

All files located directly in `lib` directory can be imported to your source
code using `#import` directive, i.e.:

    #import "{{ lib_name }}/{{ sample_file }}"
    
All library files located directly under `lib` directory has `#importonce`
declared which means they can be safely imported multiple times - only first
import is effective.

Library files with `-global.asm` suffix contains globalized elements that can
be directly accessed from root namespace. All names of these elements (except
labels) are prefixed with `c64lib_` prefix.

{% if subroutines_count > 0 %}
All library files located under `lib\sub` directory are subroutines and should
be imported only once (it's up to the programmer to ensure this) - they are
not macros so corresponding assembly code will be embedded right in the place
where they are imported.
{% endif %}
