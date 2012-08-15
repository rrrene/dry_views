# DryViews

Keep the views dry with content_for_with_default and friends!

DryViews provides extensions to ActionView::Helpers::CaptureHelper, which is part of ActionPack:
* content_for_with_default
* no_content_for
* content_for (enhanced to play nice with the above methods)

The rails rendering chain is sort of inside out so the template rendering happens first, and this is what makes it possible to override everything! The template will ALWAYS override the layout, and a partial will always override a partial layout.

Here are some guidelines for using the code below:

    - no_content_for :key
    # Use a dash (-) not equals (=)
    # Will prevent a later content_for from rendering.  This allows template overrides of layouts.

    = content_for_with_default :key
    # Use an equal (=) not a dash (-)
    # You provide it with the default HAML via a block *or* a set of params that are the same as you would pass to a standard "render" call (i.e. :partial => 'foo', :locals => {:bar => 'bar'}).

    = content_for :key
    # Use an equal (=) not a dash (-)
    # You provide it with a block, i.e. {render :partial => 'foo'}, and it will override content_for_with_default.  It has the same precedence as no_content_for, so whichever is rendered first wins, so if a layout has either no_content_for or content_for (with or without default) the template can now override it.

## Installation

Add this line to your application's Gemfile:

    gem 'dry_views'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install dry_views

## Usage

Example #1:
in a layout

    - no_content_for :breakfast                 # ARROWED!

in a template

    = content_for :breakfast do                 # WINNER!
      %h1 Dub Step

will have Dub Step

Example #2:
in a layout

    = content_for :breakfast do                 # ARROWED!
      %h1 Dub Step

in a template

    - no_content_for :breakfast                 # WINNER!

will have NOTHING

Example #3:

in a layout

    = content_for :breakfast do                 # WINNER!
      %h1 Dub Step

in a template

    = content_for :breakfast do                 # WINNER!
      %h1 Metal

will have Dub Step and Metal (Concatenation!)

Example #4:
in a layout

    = content_for_with_default :breakfast do    # ARROWED!
      %h1 Dub Step

in a template

    = content_for :breakfast do                 # WINNER!
      %h1 Metal

will have Metal only (No Dub Step!)

Example #5:

in a layout

    = content_for_with_default :breakfast do    # ARROWED!
    %h1 Dub Step

in a template

    - no_content_for :breakfast                 # WINNER!

will have NOTHING

Example #6:
in a layout

    = content_for_with_default :breakfast do    # ARROWED!
      %h1 Dub Step

in a template

    = render :partial => 'hip_hop', :layout => 'sample'
    = no_content_for :breakfast do              # ARROWED!

in the 'sample' partial layout

    = no_content_for :breakfast do              # ARROWED!

in the 'hip_hop' partial

    = content_for :breakfast do                 # WINNER!
      %h1 Hip Hop

will have Hip Hop only (No Dub Step!)

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request