# undo和redo
redo--> undo-->datafile

insert一条记录时, 表跟undo的信息都会放进 redo 中, 在commit 或之前, redo 的信息会放进硬盘上. 故障时, redo 便可恢复那些已经commit 了的数据.
 

redo解释：
>在Oracle数据库中，执行数据修改操作后，并不是马上写入数据文件，而是首先生成重做信息，并写入SGA中的一块叫LOG_BUFFER的固定区域，LOG_BUFFER的空间并不是无限大，事实上它非常小，一般设置在3～5MB左右。LOG_BUFFER有一定的触发条件，当满足触发条件后，会有相应进程将LOG_BUFFER中的内容写入一个特定类型的文件，就是传说中的联机重做日志文件。

undo->记录更改前的一份copy，但你系统rollback时，把这份copy重新覆盖到原来的数据
redo->记录所有操作，用于恢复（redo records all the database transaction used for recovery）
undo->记录所有的前印象，用于回滚（undo is used to store uncommited data infor used for rollback）
redo->已递交的事务,实例恢复时要写到数据文件去的
undo->未递交的事务.

**redo的原因是:**
每次commit时，将数据的修改立即写到online redo中，但是并不一定同时将该数据的修改写到数据文件中。因为该数据已经提交，但是只存在联机日志文件中，所以在恢复时需要将数据从联机日志文件中找出来，重新应用一下，使已经更改数据在数据文件中也改过来！

**undo的原因是:**
在oracle正常运行时，为了提高效率，假如用户还没有commit,但是空闲内存不多时，会由DBWR进程将脏块写入到数据文件中，以便腾出宝贵的内存供其它进程使用。这就是需要UNDO的原因。因为还没有发出commit语句，但是oracle的dbwr进程已经将没有提交的数据写到数据文件中去了。

undo也是datafile， 可能dirty buffer 没有写回到磁盘里面去。
只有先redo apply 成功了，才能保证undo datafile 里面的东西都是正确的，然后才能rollback.

做undo的目的是使系统恢复到系统崩溃前(关机前)的状态,再进行redo是保证系统的一致性.
不做undo,系统就不会知道之前的状态,redo就无从谈起,
所以instance crash recovery 的时候总是先rollforward， 再rollback

undo
回退段中的数据是以“回退条目”方式存储。
回退条目=块信息(在事务中发生改动的块的编号)+在事务提交前存储在块中的数据

在每一个回退段中oracle都为其维护一张“事务表”
在事务表中记录着与该回退段中所有回退条目相关的事务编号（事务SCN&回退条目）

一.undo中数据的特点：
1. 是数据修改前的备份，主要是保证用户的读一致性
2. 在事务修改数据时产生
3. 至少保存到事务结束

二.undo数据的作用：
1.回滚(rollback)操作
2.实现读一致性与闪回查询
3.从失败的事务中还原数据
4. 非正常停机后的实例恢复

三.undo回滚段的特点：
1. 回滚段是由实例自动创建用于支持事务运行的专用段，同样是区和块组成，回滚顶会按实际需要自动进行增长或收缩，是一段可以给指定事务循环使用的存储缓冲区。
2. 每个事务只会使用一个回滚段，一个回滚段在同一时刻可能会服务于多个事务
3. 当一个事务开始的时候，会指定一个回滚段，在事务进行的过程中，当数据被修改时，原始的数据会被复制到回滚段。
4. 在回滚段中，事务会不断填充盘区，直到事务结束或所有的空间被用完，如果当前的盘区不够用，事务会在段中请求扩展下一个盘区，如果所有已分配的盘区都被用完，事务会覆盖最初的盘区或者在回滚段允许的情况下扩展新的盘区来使用.
5. 回滚段存在于undo表空间中，在数据库中可以存在多个undo表空间，但同一时刻只能使用一个undo表空间。

四.回滚段中的数据类型:
回滚段中的数据主要分为以下三种：
1. Uncommitted undo information; 未提交的回滚数据，该数据所关联的事务并未提交，用于实现读一致性，所以该数据不能被其它事务的数据所覆盖
2. Committed undo information;已经提交但未过期的回滚数据，该数据关联的事务已经提交，但是仍受到undo retention参数保持时间的影响
3. Expired undo information;事务已经提交，而且数据保存时间已经超过undo retention参数指定的时间，属于已经过期的数据

当回滚段满了后，会优先覆盖Expired undo information，当过期数据空间用完后，会再覆盖Committed undo information的区域，这时undo retention参数所规定的保持时间会被破坏，Uncommitted undo information的数据是不允许覆盖的，如果要求提交的数据在undo retention参数规定的时间内不会被覆盖，可以在undo表空间上指定RETENTION GUARANTEE，语法如下：

    ALTER TABLESPACE UNDOTBS1 RETENTION GUARANTEE;

五.undo数据与redo数据的区别：
1. undo记录数据修改之前的操作，redo记录磁盘数据将要进行的操作.
2. undo用于数据的回滚操作，和实现一致性读，redo用于前滚数据库操作
3. undo存储在回滚段里，redo存储在重做日志文件里
4. undo用于在多用户并发的系统里保证一致性读，redo用于防止数据丢失

六.与undo有关的相关参数
undo_management = auto 自动的undo表空间管理
undo_tablespace = undotbs1 设置undo表空间的名称,可以存在多个undo表空间，但同时只能使用一个
undo_retention = 900(秒) 设置快照保存的最少时间，设置后在此时间段内仍有可能会被覆盖
ALTER TABLESPACE UNDO_TS RETENTION GUARANTEE; 强制所有快照必须保存 undo_retention所规定的时间。


redo
重做记录由一组“变更向量”组成。
每个变更变量中记录了事务对数据库中某个块所做的修改。
当用户提交一条commit语句时，LGWR进程会立刻将一条提交记录写入到重做日志文件中，然后再开始写入与该事务相关的重做信息。

>事务提交成功后，Oracle将为该事务生成一个系统变更码（SCN）。事务的SCN将同时记录在它的提交记录和重做记录中。

commit
提交事务前完成的工作：
·在SGA区的回退缓存中生成该事务的回退条目。在回退条目中保存有该事务所修改的数据的原始版本。
·在SGA区的重做日志缓存中生成该事务的重做记录。重做记录中记载了该事务对数据块所进行的修改，并且还记载了对回退段中的数据块所进行的修改。缓存中的重做记录有可能在事务提交之前就写入硬盘中。
·在SGA区的数据库缓丰中记录了事务对数据库所进行的修改。这些修改也有可能在事务提交之前就写入硬盘中。

提交事务时完成的工作：
1. 在为该事务指定的回退段中的内部事务表内记录下这个事务已经被提交，并且生成一个惟一的SCN记录在内部事务表中，用于惟一标识这个事务。
2. LGWR后进进程将SGA区重做日志缓存中的重做记录写入联机重做日志文件。在写入重做日志的同时还将写入该事务的SCN。
3. Oracle服务进程释放事务所使用的所有记录锁与表锁。
4. Oracle通知用户事务提交完成。
5. Oracle将该事务标记为已完成。

rollback
回退事务完成的工作：
1. Oracle通过使用回退段中的回退条目，撤销事务中所有SQL语句对数据库所做的修改。
2. Oracle服务进程释放事务所使用的所有锁
3. Oracle通知事务回退成功。
4. Oracle将该事务标记为已完成

举个例子：

insert into a(id) values(1);(redo)
这条记录是需要回滚的。
回滚的语句是delete from a where id = 1;(undo)

试想想看。如果没有做insert into a(id) values(1);(redo)
那么delete from a where id = 1;(undo)这句话就没有意义了。

现在看下正确的恢复:
先insert into a(id) values(1);(redo)
然后delete from a where id = 1;(undo)
系统就回到了原先的状态，没有这条记录了

undo表空间与redo日志文件在oracle中的作用非常重要，undo回滚段的作用与特点，同时简单介绍undo与redo的区别和各自己的作用。

一、undo中数据的特点：
1. 是数据修改前的备份，主要是保证用户的读一致性（为了实现这种功能，类似Redo，Oracle用Undo来记录前备份信息，insert、update、delete的相关信息记录在Undo表空间的回滚段内；记录的信息量，insert最少只需添加记录的rowid、update其次记录所修改的旧值，delete最多记录所删除记录的整行数据；如一事务的修改还未提交，另一事务所查询的数值会由Undo信息提供）
2. 在事务修改数据时产生
3. 至少保存到事务结束

二、undo数据的作用：
1. 回滚(rollback)操作
2. 实现读一致性与闪回查询
3. 从失败的事务中还原数据
4. 非正常停机后的实例恢复

三、undo回滚段的特点：
1. 回滚段是由实例自动创建用于支持事务运行的专用段，同样是区和块组成，回滚顶会按实际需要自动进行增长或收缩，是一段可以给指定事务循环使用的存储缓冲区
2. 每个事务只会使用一个回滚段，一个回滚段在同一时刻可能会服务于多个事务
3. 当一个事务开始的时候，会指定一个回滚段，在事务进行的过程中，当数据被修改时，原始的数据会被复制到回滚段
4. 在回滚段中，事务会不断填充盘区，直到事务结束或所有的空间被用完，如果当前的盘区不够用，事务会在段中请求扩展下一个盘区，如果所有已分配的盘区都被用完，事务会覆盖最初的盘区或者在回滚段允许的情况下扩展新的盘区来使用
5. 回滚段存在于undo表空间中，在数据库中可以存在多个undo表空间，但同一时刻只能使用一个undo表空间

四、回滚段中的数据类型:
回滚段中的数据主要分为以下三种：
1. Uncommitted undo information; 未提交的回滚数据，该数据所关联的事务并未提交，用于实现读一致性，所以该数据不能被其它事务的数据所覆盖
2. Committed undo information;已经提交但未过期的回滚数据，该数据关联的事务已经提交，但是仍受到undo retention参数保持时间的影响
3. Expired undo information;事务已经提交，而且数据保存时间已经超过undo retention参数指定的时间，属于已经过期的数据

当回滚段满了后，会优先覆盖Expired undo information，当过期数据空间用完后，会再覆盖Committed undo information的区域，这时undo retention参数所规定的保持时间会被破坏，Uncommitted undo information的数据是不允许覆盖的，如果要求提交的数据在undo retention参数规定的时间内不会被覆盖，可以在undo表空间上指定RETENTION GUARANTEE，语法如下：
ALTER TABLESPACE UNDOTBS1 RETENTION GUARANTEE

五、undo数据与redo数据的区别：
1. undo记录数据修改之前的操作（记录在回滚段中），redo记录磁盘数据将要进行的操作
2. undo用于数据的回滚操作，和实现一致性读，redo用于前滚数据库操作
3. undo存储在回滚段里，redo存储在重做日志文件里
4. undo用于在多用户并发的系统里保证一致性读，redo用于防止数据丢失

六、与undo有关的相关参数
undo_management = auto 自动的undo表空间管理
undo_tablespace = undotbs1 设置undo表空间的名称,可以存在多个undo表空间，但同时只能使用一个
undo_retention = 900(秒) 设置快照保存的最少时间，设置后在此时间段内仍有可能会被覆盖
ALTER TABLESPACE UNDO_TS RETENTION GUARANTEE; 强制所有快照必须保存undo_retention所规定的时间











