---
title: 翻译dynamic-debug-howto
date: 2020-11-18
categories:
- Linux
tags:
- Linux
- Debug
- Translation
---

1	
2	描述
3	============
4	
这篇文档记录了如何使用linux的动态调试功能。（Dynamic debug）

内核的动态调试功能已经被设计成了可以在内核代码中被动态开关，以获得更多的内核信息。
当前情况下，如果 CONFIG_DYNAMIC_DEBUG 宏被设置，那么内核中全部的all pr_debug()/dev_dbg() 和 print_hex_dump_debug()print_hex_dump_bytes() 将被开启 另端调用(per-callsite)。

如果没有设置 CONFIG_DYNAMIC_DEBUG 宏，print_hex_dump_debug() 只是 print_hex_dump(KERN_DEBUG) 的快捷方式。

For print_hex_dump_debug()/print_hex_dump_bytes(), format string is
its 'prefix_str' argument, if it is constant string; or "hexdump"
in case 'prefix_str' is build dynamically.

动态调试功能有着很多的使用功能：

22	 * Simple query language allows turning on and off debugging
23	   statements by matching any combination of 0 or 1 of:
24	
25	   - source filename
26	   - function name
27	   - line number (including ranges of line numbers)
28	   - module name
29	   - format string
30	
31	 * Provides a debugfs control file: <debugfs>/dynamic_debug/control
32	   which can be read to display the complete list of known debug
33	   statements, to help guide you

通过简单的查询语句就可以开启或关闭调试的位置，通过对应0和1的组合。







Controlling dynamic debug Behaviour
36	===================================
37	
38	The behaviour of pr_debug()/dev_dbg()s are controlled via writing to a
39	control file in the 'debugfs' filesystem. Thus, you must first mount
40	the debugfs filesystem, in order to make use of this feature.
41	Subsequently, we refer to the control file as:
42	<debugfs>/dynamic_debug/control. For example, if you want to enable
43	printing from source file 'svcsock.c', line 1603 you simply do:
44	
45	nullarbor:~ # echo 'file svcsock.c line 1603 +p' >
46					<debugfs>/dynamic_debug/control
47	
48	If you make a mistake with the syntax, the write will fail thus:
49	
50	nullarbor:~ # echo 'file svcsock.c wtf 1 +p' >
51					<debugfs>/dynamic_debug/control
52	-bash: echo: write error: Invalid argument
53	
54	Viewing Dynamic Debug Behaviour
55	===========================
56	
57	You can view the currently configured behaviour of all the debug
58	statements via:
59	
60	nullarbor:~ # cat <debugfs>/dynamic_debug/control
61	# filename:lineno [module]function flags format
62	/usr/src/packages/BUILD/sgi-enhancednfs-1.4/default/net/sunrpc/svc_rdma.c:323 [svcxprt_rdma]svc_rdma_cleanup =_ "SVCRDMA Module Removed, deregister RPC RDMA transport\012"
63	/usr/src/packages/BUILD/sgi-enhancednfs-1.4/default/net/sunrpc/svc_rdma.c:341 [svcxprt_rdma]svc_rdma_init =_ "\011max_inline       : %d\012"
64	/usr/src/packages/BUILD/sgi-enhancednfs-1.4/default/net/sunrpc/svc_rdma.c:340 [svcxprt_rdma]svc_rdma_init =_ "\011sq_depth         : %d\012"
65	/usr/src/packages/BUILD/sgi-enhancednfs-1.4/default/net/sunrpc/svc_rdma.c:338 [svcxprt_rdma]svc_rdma_init =_ "\011max_requests     : %d\012"
66	...
67	
68	
69	You can also apply standard Unix text manipulation filters to this
70	data, e.g.
71	
72	nullarbor:~ # grep -i rdma <debugfs>/dynamic_debug/control  | wc -l
73	62
74	
75	nullarbor:~ # grep -i tcp <debugfs>/dynamic_debug/control | wc -l
76	42
77	
78	The third column shows the currently enabled flags for each debug
79	statement callsite (see below for definitions of the flags).  The
80	default value, with no flags enabled, is "=_".  So you can view all
81	the debug statement callsites with any non-default flags:
82	
83	nullarbor:~ # awk '$3 != "=_"' <debugfs>/dynamic_debug/control
84	# filename:lineno [module]function flags format
85	/usr/src/packages/BUILD/sgi-enhancednfs-1.4/default/net/sunrpc/svcsock.c:1603 [sunrpc]svc_send p "svc_process: st_sendto returned %d\012"
86	
87	
88	Command Language Reference
89	==========================
90	
91	At the lexical level, a command comprises a sequence of words separated
92	by spaces or tabs.  So these are all equivalent:
93	
94	nullarbor:~ # echo -c 'file svcsock.c line 1603 +p' >
95					<debugfs>/dynamic_debug/control
96	nullarbor:~ # echo -c '  file   svcsock.c     line  1603 +p  ' >
97					<debugfs>/dynamic_debug/control
98	nullarbor:~ # echo -n 'file svcsock.c line 1603 +p' >
99					<debugfs>/dynamic_debug/control
100	
101	Command submissions are bounded by a write() system call.
102	Multiple commands can be written together, separated by ';' or '\n'.
103	
104	  ~# echo "func pnpacpi_get_resources +p; func pnp_assign_mem +p" \
105	     > <debugfs>/dynamic_debug/control
106	
107	If your query set is big, you can batch them too:
108	
109	  ~# cat query-batch-file > <debugfs>/dynamic_debug/control
110	
111	A another way is to use wildcard. The match rule support '*' (matches
112	zero or more characters) and '?' (matches exactly one character).For
113	example, you can match all usb drivers:
114	
115	  ~# echo "file drivers/usb/* +p" > <debugfs>/dynamic_debug/control
116	
117	At the syntactical level, a command comprises a sequence of match
118	specifications, followed by a flags change specification.
119	
120	command ::= match-spec* flags-spec
121	
122	The match-spec's are used to choose a subset of the known pr_debug()
123	callsites to which to apply the flags-spec.  Think of them as a query
124	with implicit ANDs between each pair.  Note that an empty list of
125	match-specs will select all debug statement callsites.
126	
127	A match specification comprises a keyword, which controls the
128	attribute of the callsite to be compared, and a value to compare
129	against.  Possible keywords are:
130	
131	match-spec ::= 'func' string |
132		       'file' string |
133		       'module' string |
134		       'format' string |
135		       'line' line-range
136	
137	line-range ::= lineno |
138		       '-'lineno |
139		       lineno'-' |
140		       lineno'-'lineno
141	// Note: line-range cannot contain space, e.g.
142	// "1-30" is valid range but "1 - 30" is not.
143	
144	lineno ::= unsigned-int
145	
146	The meanings of each keyword are:
147	
148	func
149	    The given string is compared against the function name
150	    of each callsite.  Example:
151	
152	    func svc_tcp_accept
153	
154	file
155	    The given string is compared against either the full pathname, the
156	    src-root relative pathname, or the basename of the source file of
157	    each callsite.  Examples:
158	
159	    file svcsock.c
160	    file kernel/freezer.c
161	    file /usr/src/packages/BUILD/sgi-enhancednfs-1.4/default/net/sunrpc/svcsock.c
162	
163	module
164	    The given string is compared against the module name
165	    of each callsite.  The module name is the string as
166	    seen in "lsmod", i.e. without the directory or the .ko
167	    suffix and with '-' changed to '_'.  Examples:
168	
169	    module sunrpc
170	    module nfsd
171	
172	format
173	    The given string is searched for in the dynamic debug format
174	    string.  Note that the string does not need to match the
175	    entire format, only some part.  Whitespace and other
176	    special characters can be escaped using C octal character
177	    escape \ooo notation, e.g. the space character is \040.
178	    Alternatively, the string can be enclosed in double quote
179	    characters (") or single quote characters (').
180	    Examples:
181	
182	    format svcrdma:	    // many of the NFS/RDMA server pr_debugs
183	    format readahead	    // some pr_debugs in the readahead cache
184	    format nfsd:\040SETATTR // one way to match a format with whitespace
185	    format "nfsd: SETATTR"  // a neater way to match a format with whitespace
186	    format 'nfsd: SETATTR'  // yet another way to match a format with whitespace
187	
188	line
189	    The given line number or range of line numbers is compared
190	    against the line number of each pr_debug() callsite.  A single
191	    line number matches the callsite line number exactly.  A
192	    range of line numbers matches any callsite between the first
193	    and last line number inclusive.  An empty first number means
194	    the first line in the file, an empty line number means the
195	    last number in the file.  Examples:
196	
197	    line 1603	    // exactly line 1603
198	    line 1600-1605  // the six lines from line 1600 to line 1605
199	    line -1605	    // the 1605 lines from line 1 to line 1605
200	    line 1600-	    // all lines from line 1600 to the end of the file
201	
202	The flags specification comprises a change operation followed
203	by one or more flag characters.  The change operation is one
204	of the characters:
205	
206	  -    remove the given flags
207	  +    add the given flags
208	  =    set the flags to the given flags
209	
210	The flags are:
211	
212	  p    enables the pr_debug() callsite.
213	  f    Include the function name in the printed message
214	  l    Include line number in the printed message
215	  m    Include module name in the printed message
216	  t    Include thread ID in messages not generated from interrupt context
217	  _    No flags are set. (Or'd with others on input)
218	
219	For print_hex_dump_debug() and print_hex_dump_bytes(), only 'p' flag
220	have meaning, other flags ignored.
221	
222	For display, the flags are preceded by '='
223	(mnemonic: what the flags are currently equal to).
224	
225	Note the regexp ^[-+=][flmpt_]+$ matches a flags specification.
226	To clear all flags at once, use "=_" or "-flmpt".
227	
228	
229	Debug messages during Boot Process
230	==================================
231	
232	To activate debug messages for core code and built-in modules during
233	the boot process, even before userspace and debugfs exists, use
234	dyndbg="QUERY", module.dyndbg="QUERY", or ddebug_query="QUERY"
235	(ddebug_query is obsoleted by dyndbg, and deprecated).  QUERY follows
236	the syntax described above, but must not exceed 1023 characters.  Your
237	bootloader may impose lower limits.
238	
239	These dyndbg params are processed just after the ddebug tables are
240	processed, as part of the arch_initcall.  Thus you can enable debug
241	messages in all code run after this arch_initcall via this boot
242	parameter.
243	
244	On an x86 system for example ACPI enablement is a subsys_initcall and
245	   dyndbg="file ec.c +p"
246	will show early Embedded Controller transactions during ACPI setup if
247	your machine (typically a laptop) has an Embedded Controller.
248	PCI (or other devices) initialization also is a hot candidate for using
249	this boot parameter for debugging purposes.
250	
251	If foo module is not built-in, foo.dyndbg will still be processed at
252	boot time, without effect, but will be reprocessed when module is
253	loaded later.  dyndbg_query= and bare dyndbg= are only processed at
254	boot.
255	
256	
257	Debug Messages at Module Initialization Time
258	============================================
259	
260	When "modprobe foo" is called, modprobe scans /proc/cmdline for
261	foo.params, strips "foo.", and passes them to the kernel along with
262	params given in modprobe args or /etc/modprob.d/*.conf files,
263	in the following order:
264	
265	1. # parameters given via /etc/modprobe.d/*.conf
266	   options foo dyndbg=+pt
267	   options foo dyndbg # defaults to +p
268	
269	2. # foo.dyndbg as given in boot args, "foo." is stripped and passed
270	   foo.dyndbg=" func bar +p; func buz +mp"
271	
272	3. # args to modprobe
273	   modprobe foo dyndbg==pmf # override previous settings
274	
275	These dyndbg queries are applied in order, with last having final say.
276	This allows boot args to override or modify those from /etc/modprobe.d
277	(sensible, since 1 is system wide, 2 is kernel or boot specific), and
278	modprobe args to override both.
279	
280	In the foo.dyndbg="QUERY" form, the query must exclude "module foo".
281	"foo" is extracted from the param-name, and applied to each query in
282	"QUERY", and only 1 match-spec of each type is allowed.
283	
284	The dyndbg option is a "fake" module parameter, which means:
285	
286	- modules do not need to define it explicitly
287	- every module gets it tacitly, whether they use pr_debug or not
288	- it doesn't appear in /sys/module/$module/parameters/
289	  To see it, grep the control file, or inspect /proc/cmdline.
290	
291	For CONFIG_DYNAMIC_DEBUG kernels, any settings given at boot-time (or
292	enabled by -DDEBUG flag during compilation) can be disabled later via
293	the sysfs interface if the debug messages are no longer needed:
294	
295	   echo "module module_name -p" > <debugfs>/dynamic_debug/control
296	
297	Examples
298	========
299	
300	// enable the message at line 1603 of file svcsock.c
301	nullarbor:~ # echo -n 'file svcsock.c line 1603 +p' >
302					<debugfs>/dynamic_debug/control
303	
304	// enable all the messages in file svcsock.c
305	nullarbor:~ # echo -n 'file svcsock.c +p' >
306					<debugfs>/dynamic_debug/control
307	
308	// enable all the messages in the NFS server module
309	nullarbor:~ # echo -n 'module nfsd +p' >
310					<debugfs>/dynamic_debug/control
311	
312	// enable all 12 messages in the function svc_process()
313	nullarbor:~ # echo -n 'func svc_process +p' >
314					<debugfs>/dynamic_debug/control
315	
316	// disable all 12 messages in the function svc_process()
317	nullarbor:~ # echo -n 'func svc_process -p' >
318					<debugfs>/dynamic_debug/control
319	
320	// enable messages for NFS calls READ, READLINK, READDIR and READDIR+.
321	nullarbor:~ # echo -n 'format "nfsd: READ" +p' >
322					<debugfs>/dynamic_debug/control
323	
324	// enable messages in files of which the paths include string "usb"
325	nullarbor:~ # echo -n '*usb* +p' > <debugfs>/dynamic_debug/control
326	
327	// enable all messages
328	nullarbor:~ # echo -n '+p' > <debugfs>/dynamic_debug/control
329	
330	// add module, function to all enabled messages
331	nullarbor:~ # echo -n '+mf' > <debugfs>/dynamic_debug/control
332	
333	// boot-args example, with newlines and comments for readability
334	Kernel command line: ...
335	  // see whats going on in dyndbg=value processing
336	  dynamic_debug.verbose=1
337	  // enable pr_debugs in 2 builtins, #cmt is stripped
338	  dyndbg="module params +p #cmt ; module sys +p"
339	  // enable pr_debugs in 2 functions in a module loaded later
340	  pc87360.dyndbg="func pc87360_init_device +p; func pc87360_find +p"