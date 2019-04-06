---
title: Create HTML from output of Perl::Critic
author: Rob Earl
type: post
date: 2012-09-22T07:46:00+00:00
excerpt: |
  Perl::Critic is great. If you haven't tried it, you should. It has helped me improve the quality of my code no end.
  
  I wrote this script to make it easier to view the feedback in the context of the code itself. When run in a directory containing Perl&hellip;
url: /index.php/webdev/perl/create-html-from-output-of/
views:
  - 7285
rp4wp_auto_linked:
  - 1
categories:
  - Perl
tags:
  - code quality
  - critic
  - html
  - perl
  - perlcritic

---
[Perl::Critic][1] is great. If you haven&#8217;t tried it, you should. It can help you improve the quality of your code no end.

I wrote this script to make it easier to view the feedback in the context of the code itself. When run in a directory containing Perl code it&#8217;ll create a critic_html directory containing index.html summarizing the results:

![critic_html/index.html][2]

Clicking through to any of the files will give you all the violations found by Perl::Critic, inline with the code:

![][3]

In addition to Perl::Critic, the script uses Template::Toolkit to format the output. The code below and templates can be found in the attached [critic_html.zip][4]

<pre>#!/usr/bin/perl

use strict;
use warnings;

use Perl::Critic;
use Perl::Critic::Utils;

use Template;
use Cwd qw(abs_path);
use File::Basename;
use English qw(-no_match_vars);

use constant SEVERITY  =&gt; 1; # Include all violations.
use constant SKIP_GOOD =&gt; 0; # Skip files with no violations?

mkdir 'critic_html';
mkdir 'critic_html/src';

use autodie;

# Analyse all perl files below the current directory.
my @files = Perl::Critic::Utils::all_perl_files(q{.});

my @summary = (); # Store statistics on each file processed.

foreach my $file (@files) {
    # Create a new Critic for per file statistics.
    my $critic = Perl::Critic-&gt;new( '-severity' =&gt; SEVERITY );

    my $file_safe = $file;
    $file_safe =~ s/[W]/_/g;

    my @violations = $critic-&gt;critique($file);
    next if (!@violations && SKIP_GOOD);

    push @summary, { 'filename' =&gt; $file,
                     'link'     =&gt; "src/$file_safe.html",
                     'stats'    =&gt; $critic-&gt;statistics() };

    open my $FH, '&lt;', $file;
    my @lines = &lt;$FH&gt;;
    close $FH;

    # Attach all violations to the line they were found on.
    my @violations_by_line = ();
    my $line_number = 1;
    foreach my $line (@lines) {
        # Get all the violations for the current line.
        my @line_violations = grep { $_-&gt;line_number() == $line_number} @violations;
        push @violations_by_line, { 'number'  =&gt; $line_number,
                                    'content' =&gt; $line,
                                    'violations' =&gt; @line_violations };
        $line_number++;
    }

    write_html("critic_html/src/$file_safe.html", 'codefile', { 'title' =&gt; "Critic Analysis of $file",
                                                                'lines' =&gt; @violations_by_line } );
}

write_html('critic_html/index.html', 'index', { 'title' =&gt; 'Perl::Critic::HTML Summary',
                                                'files' =&gt; @summary });

sub write_html {
    my ($filename, $template, $data) = @_;

    # Include templates from the install directory.
    my $tt = Template-&gt;new({ 'INCLUDE_PATH' =&gt; dirname(abs_path($PROGRAM_NAME)).'/templates' } );
    print "Writing $filenamen";
    open my $FILE, '&gt;', $filename;
    $tt-&gt;process($template, $data, $FILE);
    close $FILE;

    return;
}</pre>

 [1]: http://search.cpan.org/~thaljef/Perl-Critic-1.118/lib/Perl/Critic.pm
 [2]: /wp-content/uploads/blogs/WebDev/CriticHtml/critic-html-index.jpg ""
 [3]: /wp-content/uploads/blogs/WebDev/CriticHtml/critic-html-code.jpg ""
 [4]: /wp-content/uploads/blogs/WebDev/CriticHtml/critic_html.zip