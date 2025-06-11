---
date: '2025-06-11T04:38:50Z'
created_at: '2025-06-11T04:38:50Z'
updated_at: '2025-06-11T04:38:50Z'
title: fzf files sorted by newest
slug: fzf-files-sorted-by-newest
---
Today I ran <code>hugo new</code> to create a file, and then wanted to edit it. I have <code>fzf</code> set up to let me open files in Vim, but suddenly realized… why doesn’t the file I just created show up as the first option in <code>fzf</code>? Apparently the answer is that it’s really annoying to get a recursive list of files and then sort them by creation date, to the point where <a href="https://www.reddit.com/r/commandline/comments/t2lh0w/browse_in_fzf_and_select_file_in_reverse/">a Reddit post asking my exact question</a> had no answers.

It took a while to dig around in various different tools’ docs and repos, but I eventually landed on using <code>rg</code> to list files. This does exactly what I wanted, and I’m really happy with it:

<pre><code>FZF_DEFAULT_COMMAND='rg --files --sortr created' fzf --tmux --print0 | xargs -0 -o $EDITOR
</code></pre>

Here’s a breakdown:

<ul>
	<li><code>rg --files</code>  is how you ask <code>ripgrep</code> to list all files in the current directory, recursively (like <code>find . -type f</code>, but also respecting <code>.gitignore</code>)</li>
	<li><code>--sortr created</code> is how you tell <code>ripgrep</code> that it should take every single file it finds and reverse-sort the full list by creation date (that’s how the newest file is the first result inside <code>fzf</code>)</li>
	<li><code>--print0</code> make sure that fzf will escape file boundaries with a null, in case of special characters in the file name and/or multiple files selected</li>
	<li><code>xargs -0 -o</code> not only tells xargs to use null separators, but the <code>-o</code> is a non-POSIX BSD extension (so it’s in macOS as well) that tells xargs to reopen stdin as /dev/tty inside the process it’s about to run. that makes it possible to interact with the editor, if you use a CLI editor</li>
</ul>


