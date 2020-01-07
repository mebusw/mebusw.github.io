title: SVN pre-commit hook
id: 158
categories:
  - agile
  - tools
date: 2014-02-08 23:00:09
tags:
---

某团队希望做到Continuous Code Review, 想在每次check-in 到SVN之前，先判断特定用户群体否在commit log里面包含了"Review By: xxx"的字样。

记得以前NSN里面有人用过这个法子，记不太清了。

于是研究了一下脚本，其实SVN/GIT都提供了类似的hook, 在&lt;your repository&gt;/hooks 目录下，都是shell或cmd脚本（要看服务器的操作系统了），会在不同的事件时触发。

为了实现更复杂的功能或者需要跨平台，那不妨用shell或cmd去调用Python脚本咯。

<!--more-->

SVN的基本原理就是用"svnlook"命令来查看服务器的元数据，比如"svnlook info REPOS_PATH --revision 23“（见[http://i18n-zh.googlecode.com/svn/www/svnbook-1.4/svn.ref.svnlook.c.info.html](http://i18n-zh.googlecode.com/svn/www/svnbook-1.4/svn.ref.svnlook.c.info.html)）

其中 REPOS_PATH是服务器上的绝对路径。--revision后面跟已有版本号码，而--transaction后面跟提交事务ID，也就是我们要利用的。

&nbsp;

shell脚本如下，其中路径根据需要自行修改：
<pre># pre-commit.sh
# %1 is REPOS_PATH passed in by svn server
# %2 is transaction ID passed in by svn server
python ./pre-commit.py %1 %2</pre>
&nbsp;

Python脚本根据网上的例子修改，除了检查commit log，还可以通过”svnlook changed“命令来检查改动文件的内容。
<pre>#!/bin/env python
" Example Subversion pre-commit hook. "</pre>
<pre>def command_output(cmd):
 " Capture a command's standard output. "
 import subprocess
 return subprocess.Popen(
 cmd.split(), stdout=subprocess.PIPE).communicate()[0]</pre>
<pre>def files_changed(look_cmd):
 """ List the files added or updated by this transaction.</pre>
<pre>"svnlook changed" gives output like:
 U trunk/file1.cpp
 A trunk/file2.cpp
 """
 def filename(line):
 return line[4:]
 def added_or_updated(line):
 return line and line[0] in ("A", "U")
 return [
 filename(line)
 for line in command_output(look_cmd % "changed").split("\n")
 if added_or_updated(line)]</pre>
<pre>def txn_log(look_cmd):
 """ Print the author, datestamp, log message size (in bytes), 
 and log message, followed by a newline character.</pre>
<pre>"svnlook info" gives output like:
 sally
 2003-02-22 17:44:49 -0600 (Sat, 22 Feb 2003)
 16
 Rearrange lunch.
 """
 print look_cmd
 lines = command_output(look_cmd % "info").splitlines()
 return [lines[0], ''.join(lines[3:])]</pre>
<pre>
def file_contents(filename, look_cmd):
 " Return a file's contents for this transaction. "
 return command_output(
 "%s %s" % (look_cmd % "cat", filename))</pre>
<pre>def contains_tabs(filename, look_cmd):
 " Return True if this version of the file contains tabs. "
 return "\t" in file_contents(filename, look_cmd)</pre>
<pre>def check_cpp_files_for_tabs(look_cmd):
 " Check C++ files in this transaction are tab-free. "
 def is_cpp_file(fname):
 import os
 return os.path.splitext(fname)[1] in ".cpp .cxx .h".split()
 cpp_files_with_tabs = [
 ff for ff in files_changed(look_cmd)
 if is_cpp_file(ff) and contains_tabs(ff, look_cmd)]
 if len(cpp_files_with_tabs) &gt; 0:
 sys.stderr.write("The following files contain tabs:\n%s\n"
 % "\n".join(cpp_files_with_tabs))
 return len(cpp_files_with_tabs)</pre>
<pre>def check_changed_file():
 usage = """usage: %prog REPOS TXN</pre>
<pre>Run pre-commit options on a repository transaction."""
 from optparse import OptionParser
 parser = OptionParser(usage=usage)
 parser.add_option("-r", "--revision",
 help="Test mode. TXN actually refers to a revision.",
 action="store_true", default=False)
 errors = 0
 try:
 (opts, (repos, txn_or_rvn)) = parser.parse_args()
 look_opt = ("--transaction", "--revision")[opts.revision]
 look_cmd = "svnlook %s %s %s %s" % (
 "%s", repos, look_opt, txn_or_rvn)
 errors += check_cpp_files_for_tabs(look_cmd)
 except:
 parser.print_help()
 errors += 1
 return errors</pre>
<pre>def check_txn_log():
 usage = """usage: %prog REPOS</pre>
<pre>Run pre-commit options on a repository last log."""
 from optparse import OptionParser
 parser = OptionParser(usage=usage)
 errors = 0
 try:
 (opts, (repos)) = parser.parse_args()
 print parser.parse_args()
 look_cmd = "svnlook %s -t %s %s " % (
 "%s", repos[1], repos[0])
 (user, message) = txn_log(look_cmd)
 print user
 print message</pre>
<pre>user_list = ['jacky', 'jimmy']
 if not 'review by' in message.lower() and user in user_list:
 errors = 33</pre>
<pre>
 except:
 parser.print_help()
 errors = 1
 return errors</pre>
<pre>if __name__ == "__main__":
 import sys
 sys.exit(check_txn_log())</pre>