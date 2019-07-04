# 1. Test Objective  
The document is to introduce how to test shell_long via CTP. It is similiar with shell test with CTP.For some shell cases, it spend so much time which affects the end of shell regression test, then remove them to shell_long test catagary.    

# 2. Tools Introduction  
## 2.1 CTP tools
Please refer to [README.md](https://github.com/CUBRID/cubrid-testtools/blob/develop/CTP/README.md) of CTP  
the CTP usage for shell_long is as below
```bash
ctp.sh shell -c ~/CTP/conf/shell_runtime.conf
```
```
BUILD_SCENARIOS="shell_long"
shell_config_template=${CTP_HOME}/conf/shell_template_for_${BUILD_SCENARIOS}.conf
shell_fm_test_conf="${CTP_HOME}/conf/shell_runtime.conf"
cp -f ${shell_config_template} ${shell_fm_test_conf}
ctp.sh shell -c $shell_fm_test_conf
```
## 2.2 Common Tools
Common tool means the tool which is used by many test suits. For common tools, cubrid_common,cubrid_scheduler, core_analyzer are used in shell_long test for legacy,but now they have been integarated into CTP.
```bash
cd ~/CTP/common/script
$ ls -l |awk '{print $NF}' 
248
analyze_failure.sh
analyzer.sh
commit_config_file
convert_to_git_url.sh
crash_template_ha.sh
crash_template_single.sh
file_core_issue.sh
gdb_core.sh
generate_build_test.sh
issue.sh
kill_process_tree.sh
prepare_memory_env.sh
process_safe.sh
report_issue.sh
run_action_files
run_coverage_collect_and_upload
run_cubrid_install
run_download
run_general_feedback
run_git_update
run_grepo_fetch
run_mail_send
run_remote_script
run_sparsefs
run_svn_update
run_upload
sender.sh
start_consumer.sh
start_grepo_server.sh
start_producer.sh
stop_consumer.sh
upgrade.sh
util_common.sh
util_compat_test.sh
util_file_param.sh
util_filter_supported_parameters.sh
util_install_build.sh
```

# 3. Deploy shell_long Regression Test
In this section, we will introduce how to deploy the Shell_long Regression Test Environment.  
## 3.1 Test Machines
For Shell_long regression test, we usually have one controller node and multiple test nodes.In our regression configurations,we use five test nodes.  
**Controller node** : This node listens to test messages and starts a test when there is a test message.  
**Test node** : CUBRID server is deployed on this node,we execute test cases on it. 

Information about each test machines.
<table>
<tr>
<th>Description</th>
<th>User Name</th>
<th>IP</th>
<th>Hostname</th>
<th>Tools to deploy</th>
</tr>
<tr class="even">
<td>controller node</td>
<td>rqgcontroller</td>
<td>192.168.1.99</td>
<td>func24</td>
<td><p>CTP</p>
</td>
</tr>
<tr class="even">
<td>test node</td>
<td>shell_long</td>
<td>192.168.1.99</td>
<td>func24</td>
<td><p>CTP</p>
<p>cubrid-testcases-private</p>
</td>
</tr>
<tr class="even">
<td>test node</td>
<td>shell_long</td>
<td>192.168.1.100</td>
<td>func25</td>
<td><p>CTP</p>
<p>cubrid-testcases-private</p>
</td>
</tr>
<tr class="even">
<td>test node</td>
<td>shell_long</td>
<td>192.168.1.101</td>
<td>func26</td>
<td><p>CTP</p>
<p>cubrid-testcases-private</p>
</td>
</tr>
<tr class="even">
<td>test node</td>
<td>shell_long</td>
<td>192.168.1.102</td>
<td>func27</td>
<td><p>CTP</p>
<p>cubrid-testcases-private</p>
</td>
</tr>
<tr class="even">
<td>test node</td>
<td>shell_long</td>
<td>192.168.1.103</td>
<td>func28</td>
<td><p>CTP</p>
<p>cubrid-testcases-private</p>
</td>
</tr>
</table>

## 3.2	Test Deployments
### For all nodes
1. Install JDK
2. Add IP and hostname to /etc/hosts
```
192.168.1.99  func24  
192.168.1.100  func25    
192.168.1.101  func26  
192.168.1.102  func27  
192.168.1.103  func28   
```
3. checkout git repositories to $HOME/
```
git clone https://github.com/CUBRID/cubrid-testtools.git
cd ~/cubrid-testtools/
git checkout develop
```
### On Controller node
1. Install CTP  
Please follow [the guide to install CTP](http://jira.cubrid.org/browse/CUBRIDQA-835?focusedCommentId=4753733&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-4753733).  
2. touch ~/CTP/conf/shell_template_for_shell_long.conf
Here is the config file that we use for current daily QA test:
 ```
$ cat shell_template_for_shell_long.conf 
default.cubrid.cubrid_port_id=1568
default.broker1.BROKER_PORT=30090
default.broker1.APPL_SERVER_SHM_ID=30090
default.broker2.BROKER_PORT=33091
default.broker2.APPL_SERVER_SHM_ID=33091
default.ha.ha_port_id=59909

env.99.ssh.host=192.168.1.99
env.99.ssh.port=22
env.99.ssh.user=shell_long
env.99.ssh.pwd=******

env.100.ssh.host=192.168.1.100
env.100.ssh.port=22
env.100.ssh.user=shell_long
env.100.ssh.pwd=******

env.101.ssh.host=192.168.1.101
env.101.ssh.port=22
env.101.ssh.user=shell_long
env.101.ssh.pwd=******

env.102.ssh.host=192.168.1.102
env.102.ssh.port=22
env.102.ssh.user=shell_long
env.102.ssh.pwd=******

env.103.ssh.host=192.168.1.103
env.103.ssh.port=22
env.103.ssh.user=shell_long
env.103.ssh.pwd=******


scenario=${HOME}/cubrid-testcases-private/longcase/shell
test_continue_yn=false
cubrid_download_url=http://127.0.0.1/REPO_ROOT/store_02/10.1.0.6876-f9026f8/drop/CUBRID-10.1.0.6876-f9026f8-Linux.x86_64.sh
testcase_exclude_from_file=${HOME}/cubrid-testcases-private/longcase/shell/config/daily_regression_test_excluded_list_linux.conf
testcase_update_yn=true
testcase_git_branch=develop
#testcase_timeout_in_secs=604800
test_platform=linux
test_category=shell_long
testcase_exclude_by_macro=LINUX_NOT_SUPPORTED
testcase_retry_num=0
delete_testcase_after_each_execution_yn=false
enable_check_disk_space_yn=true

feedback_type=database
feedback_notice_qahome_url=http://192.168.1.86:8080/qaresult/shellImportAction.nhn?main_id=<MAINID>


owner_email=Orchid<lanlan.zhan@navercorp.com>
cc_email=CUBRIDQA<dl_cubridqa_bj_internal@navercorp.com>

git_user=cubridqa
git_email=dl_cubridqa_bj_internal@navercorp.com
git_pwd=******

feedback_db_host=192.168.1.86
feedback_db_port=33080
feedback_db_name=qaresu
feedback_db_user=dba
feedback_db_pwd=
```
When you need to test shell_long_debug, then need to copy ~/CTP/conf/shell_template_for_shell_long.conf as~ /CTP/conf/shell_template_for_shell_long_debug.conf  

3. touch start_test.sh  
```
 cat ~/start_test.sh

# If only need to listen the shell_long test message
# nohup start_consumer.sh -q QUEUE_CUBRID_QA_SHELL_LONG_LINUX -exec run_shell &

# We use one controllar to listening shell_heavy, shell_long, and RQG test messages in dailyqa.
nohup start_consumer.sh -q QUEUE_CUBRID_QA_SHELL_HEAVY_LINUX,QUEUE_CUBRID_QA_RQG,QUEUE_CUBRID_QA_SHELL_LONG_LINUX -exec run_shell,run_shell,run_shell &   
```
 4. Update ~/.bash_profile
 ```
 $ cat .bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin

export PATH

export DEFAULT_BRANCH_NAME=develop
export CTP_HOME=$HOME/CTP
export CTP_BRANCH_NAME=develop
export CTP_SKIP_UPDATE=0

export CTP_HOME=~/CTP
export init_path=$CTP_HOME/shell/init_path
export RQG_HOME=~/random_query_generator
PATH=$JAVA_HOME/bin:$HOME/CTP/common/script:$PATH
```
 
5. start consumer process  
```bash
 sh start_test.sh
```

### On Test nodes
1. Install CTP  
Please follow [the guide to install CTP](http://jira.cubrid.org/browse/CUBRIDQA-835?focusedCommentId=4753733&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-4753733).
2. Check out test cases from git to $HOME/
```
cd ~
git clone https://github.com/CUBRID/cubrid-testcases-private.git 
cd ~/cubrid-testtools-internal/
git checkout develop
```
3. Install CUBRID.

4. Add following settings to ~/.bash_profile and source it.
```bash
$ cat .bash_profile
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin

export PATH

export LC_ALL=en_US

export CTP_HOME=$HOME/CTP
export LCOV_HOME=/usr/local/cubridqa/lcov-1.11

export init_path=$HOME/CTP/shell/init_path

export GCOV_PREFIX=$HOME
export GCOV_PREFIX_STRIP=2

export CTP_BRANCH_NAME="develop"
export CTP_SKIP_UPDATE="0"

export PATH=$CTP_HOME/bin:$CTP_HOME/common/script:$CUBRID/bin:$LCOV_HOME/bin:$JAVA_HOME/bin:$PATH:/usr/local/sbin:/usr/sbin

ulimit -c unlimited

#-------------------------------------------------------------------------------
# set CUBRID environment variables
#-------------------------------------------------------------------------------
. ~/.cubrid.sh
ulimit -c unlimited
```
# 4. Regression Tests
We perform shell_long test twice a week (actually it is controlled through crontab) and perform code coverage test for monthly.  
crontab task for shell_long test:
```
#######################################################################
job_shell_long.service=ON
job_shell_long.crontab=* 0 11 ? * SUN,THU
job_shell_long.listenfile=CUBRID-{1}-linux.x86_64.sh
job_shell_long.acceptversions=10.0.*.0~8999,10.1.*,10.2.*
job_shell_long.package_bits=64
job_shell_long.package_type=general

job_shell_long.test.1.scenario=shell_long
job_shell_long.test.1.queue=QUEUE_CUBRID_QA_SHELL_LONG_LINUX

#######################################################################
```


## 4.1 Daily regression test
When the build server has a new build, a shell_long test will be executed. If there is something wrong and need to run shell_long test again, you can send a test message. 
### Send a test message
1. go to controller node, check `~/CTP/conf/shell_template_for_shell_long.conf`. This config file is only for regression test. We usually don't modify it except we have new test nodes.
2. login `message@192.168.1.91` and send test message.  
By default, it will find the `~/CTP/conf/shell_template_for_shell_long.conf` to execute. Otherwise it will find the `~/CTP/conf/shell_template.conf`   
```bash
sender.sh QUEUE_CUBRID_QA_SHELL_LONG_LINUX http://192.168.1.91:8080/REPO_ROOT/store_01/10.2.0.8268-6e0edf3/drop/CUBRID-10.2.0.8268-6e0edf3-Linux.x86_64.sh shell_long default
```

## 4.2 Verify test Results
### Check if there is the test result
Go to QA homepage and click the CI build, wait for the page loading, see the 'Function' tab and find the shell_long
result.  
![verify_func_tab](./doc/media/image1.png)   
The category `shell_long` links to the current completed test cases  
![completed_test_cases](./doc/media/image2.png)  
The numbers of `Fail` tag links to the failures, it includes total failures and new failures of this build compared with previous build.      
![fail_test_cases](./doc/media/image3.png)    
If it shows **'NO RESULT (OR RUNNING)'** as bellow, maybe you can check whether the crontab task time has not arrived or the test environments have problem.      
![verify_no_result](./doc/media/image4.png)    
Here's what you might encounter:    
* Test message is in the queue(test is not started) 
  Sometimes there is another test executed such as RQG test,shell_heavy test or code coverage test. In this case, just wait for other test finish.  
* Insufficient disk space  
  Some test case executed failed ,and on `Total` tag  of `Fail` column, we may see `Server crash graphic identifier`
![Server_crash_graphic_identifier](./doc/media/image5.png)      
* Test is not finished  
    * Sometimes there is another test executed such as RQG test,shell_heavy test or code coverage test. In this case, just wait for the test finish and then verify the results.  
    * If the CI build comes on time as usual, then you need to check why the test is so slow. It may because there is a server crash, server hangs up, or performance drop. In this case, you need to open a bug.  
4.2 Verify test Results
Result overview
qahome -> select build number ->choose tab ‘Performance’
result overview Then, select a build as baseline to compare the current result to it.
result_comparation
Usually, in daily regression test, we choose the previous CI build as baseline.

Check the configurations
check conf1 Click the red part to confirm whether the configurations of this test is expected.
check conf2

Check the resource utilization
check_resource1 Click the link under‘Resource Utilization’ to check the resource utilization of the test.

Check the resource graph check_resource2
Check the raw data if necessary
check_resource3 Click ‘Raw data for Test’in the picture above, and go to this page:
check_resource4 In this page, we can see the raw data which is generated in the test.
Server and broker error logs
Login the server and broker machines, check the logs.
The logs are remained at ~/dblog/, for example, ‘dblog/10.2.0.8335-45cec1b_M20190416033710/log’.
Check whether there is any ‘ERROR’in the logs.
Error that can be ignored most of the time:
ERROR_CODE = -670 (Operation would have caused one or more unique constraint violations)
If the count of this error is not too much (such as less than 100), you can ignore it.

4.3	Report issues
Crash issue
If there is a crash, report it following the rules in this link: ‘How to Report Regression Crash Issues’

Performance issue
If the performance is dropped on the test build, retest this build to confirm the result. Then report it on jira.

# 5.Test Case Specification
## Requirements
export init_path=$HOME/CTP/shell/init_path

## How To Write Test Case    
It is the same with shell test cases  
   * Test cases: The file extension is ``.sh``, and it is located in ``cases`` subdirectory, naming rule: ``/path/to/test_name/cases/test_name.sh``  
   eg: cubrid-testcases-private/longcase/shell/5hour/bug_bts_4707/cases/bug_bts_4707.sh  
   * Example for reference  
    
     ```
     #!/bin/sh
     # to initialize the environment variables which are required by case
     . $init_path/init.sh
     # to support RQG regression, the environment variables and functions in rqg_init.sh should be initialized, so just need uncomment the below statement($init_path/rqg_init.sh), 
     # if you want to understand the functions which will be used in RQG case, please refer to $init_path/rqg_init.sh  
     # . $init_path/rqg_init.sh
     init test
     dbname=tmpdb

     cubrid_createdb $dbname
	
     dosomethings
     ...
	
     if [condition]
     then
	        # Print testing result according to the condition, PASS means ok, otherwise nok
	        write_ok
     else
	        write_nok
     fi
	
     cubrid server stop $dbname
     cubrid broker stop
	
     cubrid deletedb $dbname
     # clean environment
     finish
	  ```
## Example Test Cases
```
$ cat bug_bts_14571.sh 
#!/bin/sh
. $init_path/init.sh
init test
set -x

dbname=db14571
cubrid server stop $dbname
cubrid broker stop 
cubrid deletedb $dbname
rm *.log *.err

cubrid_createdb $dbname --db-volume-size=20M --log-volume-size=20M
cubrid start server $dbname
cubrid broker start 

csql -u dba $dbname -i schema.sql

if [ "$OS" != "Linux" ]; then
        xgcc -o prepare prepare.c -I${CUBRID}/include -L${CUBRID}/bin -L${CUBRID}/lib -lcascci ${MODE}
else
        xgcc -o prepare prepare.c -I${CUBRID}/include -L${CUBRID}/lib -lcascci ${MODE}
fi

port=`get_broker_port_from_shell_config`
./prepare localhost $port $dbname

csql -udba $dbname -c "create index idx_1 on t1 (a)" > result_1.log 2> error_1.log &
sleep 3
csql -udba $dbname -c "create index idx_2 on t1 (b)" > result_2.log 2> error_2.log &
sleep 60
csql -udba $dbname -c "select * from db_index where class_name = 't1'" > result_3.log

if test -s error_1.log; then
  write_nok
else
   write_ok
fi

if test -s error_2.log; then
  write_nok
else
   write_ok
fi

sed -i 's/[^(]*[$)]//g' error_2.log
format_csql_output result_3.log
compare_result_between_files result_3.log bug_bts_14571.answer

cubrid service stop $dbname
if [ `cat bug_bts_14571.result | grep NOK | wc -l ` -ne 0 ] && [ $OS == Windows_NT ]
then
    curr_time=$(date '+%Y-%m-%d-%H-%M-%S')
    mkdir -p $TEST_BIG_SPACE/../bug_bts_14571_$curr_time
    cp -rf $CUBRID $TEST_BIG_SPACE/../bug_bts_14571_$curr_time
    cp -rf ../../bug_bts_14571 $TEST_BIG_SPACE/../bug_bts_14571_$curr_time
fi

cubrid deletedb $dbname
finish  
```
    
## Common function in $init_path/init.sh for shell_long cases  
* cubrid_createdb $dbname  
Sometimes CUBRID need charset to create databases,eg:      
For CUBRID 9 or higher: cubrid createdb testdb en_us  
For CUBRID 8 or lower: cubrid createdb testdb  

```
function cubrid_createdb()
{
    ##parse build version
    parse_build_version
    if [ $cubrid_major -ge 9 -a $cubrid_minor -gt 1 ] || [ $cubrid_major -ge 10 ]
    then
        cubrid createdb $* $CUBRID_CHARSET
    else
        cubrid createdb $*
    fi
}

```


* get_broker_port_from_shell_config  
Sometimes we can not get broker port from `cubrid broker status -b |grep broker1 |awk '{print $4}'`.    
We just get port from shell_config.xml,since the $CUBRID/conf/cubrid_broker.conf will be updated as shell_config.xml configured.      
```
# get broker port from shell_config.xml
function get_broker_port_from_shell_config
{
  port=`awk '/<port>/' $init_path/shell_config.xml`
  port=${port#*>}
  port=${port%<*}
  echo $port
}

```
* format_csql_output  
The result of csql execution may have some info about time, it is unstable,we need format this result.    
The saved answer file can not appear unstable info,sush as time,ip and so on.  
```
function format_csql_output
{
    if [ -n "$1" ];then
        #sed -e '/SQL statement execution time/d' $1 > outputtmp.txt
        #sed  "s/(.* sec)//g" $1 > outputtmp.txt
        sed -i '/SQL statement execution time/d' $1
        sed -i '/(.* sec)/d' $1
        sed -i '/Committed./d' $1
    else
        echo "Please input an file name"
    fi
}
```
* compare_result_between_files  
We can use this function to compare test results with expected results.   
Refer to [Example Test Cases](#Example-Test-Cases)    
`compare_result_between_files result_3.log bug_bts_14571.answer`    
bug_bts_14571.answer is the expect answer which we have saved,once the actual result is different from the expected result,the
test cases run failed.we need to find fail reason.  

```
function compare_result_between_files
{

  cci_driver=""
  server=""

  if [ $# -lt 2 ]
  then
     write_nok "Please input two files to compare"
     return
  fi

  if [ -f $CUBRID/qa.conf ]
  then
     cci_driver=`grep 'CCI_Version' $CUBRID/qa.conf|awk -F= '{print $2}'`
     server=`grep 'Server_Version' $CUBRID/qa.conf|awk -F= '{print $2}'`
  fi

  left=`get_best_compat_file $1 $server $cci_driver`
  right=`get_best_compat_file $2 $server $cci_driver`

  if [ "$left" == "" ]
  then
     write_nok "Cannot find the proper file for $1 to compare"
     return
  fi

  if [ "$right" == "" ]
  then
     write_nok "Cannot find the proper file for $2 to compare"
     return
  fi

  dos2unix $left
  dos2unix $right

  echo "start to compare files: diff $left $right"  
  if [ "$3" = "error" ]
  then
        if diff $left $right -b
        then
                write_nok
                echo "diff $left $right failed" >> $result_file
                #diff $left $right -y >> $result_file
                diff $left $right -y |tee -a $result_file
        else
                write_ok
        fi
        let "answer_no = answer_no + 1"
  else
        if diff $left $right -b
        then
                write_ok
        else
                write_nok
                echo "diff $left $right failed" >> $result_file
                #diff $left $right -y >> $result_file
                diff $left $right -y |tee -a $result_file
        fi
        let "answer_no = answer_no + 1"
  fi
}
```
* write_ok and write_nok  
When the result meets expectations,we use write_ok,otherwise we use write_nok.   
Refer to [Example Test Cases](#Example-Test-Cases)    
```
function write_ok 
{
  if [ -z "$1" ]
  then
        echo "----------------- $case_no : OK"
        echo "$case_name-$case_no : OK" >>  ${cur_path}/$result_file
        let "case_no = case_no + 1"
  else
        echo "----------------- $case_no : OK " $1
        echo "$case_name-$case_no : OK " $1 >>  ${cur_path}/$result_file
        let "case_no = case_no + 1"
  fi
}

```
```
function write_nok
{
  if [ -z "$1" ]
  then
        echo "----------------- $case_no : NOK"
        echo "$case_name-$case_no : NOK" >> ${cur_path}/$result_file
        internal_err=`grep "Internal Error" $CUBRID/log/server/*.err | wc -l`
        if [ $internal_err -gt 0 ]
        then
            grep "Internal Error" $CUBRID/log/server/*.err >> ${cur_path}/$result_file
        fi
        let "case_no = case_no + 1"
  elif [ -f "$1" ];
  then
        echo "$case_name-$case_no : NOK"  >> ${cur_path}/$result_file
        cat $1 >> ${cur_path}/$result_file
        let "case_no = case_no + 1"
  else
        echo "----------------- $case_no : NOK" $1
        echo "$case_name-$case_no : NOK" $1 >> ${cur_path}/$result_file
        let "case_no = case_no + 1"
  fi
}
```

* finish  
After the test have completed,we need clear test environment to avoid affect other shell cases  
Refer to [Example Test Cases](#Example-Test-Cases)  
```
function finish {
#  rm -f *.err *.log >/dev/null 2>&1
  rm -f userver.err.* >/dev/null 2>&1
  rm -f uclient.err.* >/dev/null 2>&1
  rm -f client.err.* >/dev/null 2>&1
  rm -rf ./lob
  cubrid service stop
  pkill cub >/dev/null 2>&1
  if [ $need_count_time -eq 1 ]; then
        count_time
  fi
  release_broker_sharedmemory
  delete_ini
  restore_all_conf
  echo "[INFO] TEST STOP (`date`)"
}
```
*Notice: *

## Daily Shell_long Test Cases Path  
Path: cubrid-testcases-private/longcase/shell  
```
$ find ./ -name "*.sh"                              
./5hour/bug_bts_16594/cases/bug_bts_16594.sh
./5hour/bug_bts_4707/cases/bug_bts_4707.sh
./5hour/bug_bts_7288/cases/bug_bts_7288.sh
./5hour/bug_bts_16581/cases/bug_bts_16581.sh
./5hour/bug_bts_6753_2/cases/bug_bts_6753_2.sh
./5hour/bug_bts_6785/cases/bug_bts_6785.sh
./5hour/bug_bts_6396/cases/bug_bts_6396.sh
./1hour/bug_bts_15629/cases/bug_bts_15629.sh
./1hour/bug_bts_15419_1_asc/cases/bug_bts_15419_1_asc.sh
./1hour/bug_bts_5222/cases/bug_bts_5222.sh
./1hour/bug_bts_17881/cases/bug_bts_17881.sh
./1hour/bug_bts_18031/cases/bug_bts_18031.sh
./1hour/bug_bts_9382/cases/bug_bts_9382.sh
./1hour/bug_bts_15419_1_desc/cases/bug_bts_15419_1_desc.sh
./1hour/bug_bts_15419_2_asc/cases/bug_bts_15419_2_asc.sh
./1hour/bug_bts_7350/cases/bug_bts_7350.sh
./1hour/_02_cursor_stress/cases/_02_cursor_stress.sh
./1hour/bug_bts_14602/cases/bug_bts_14602.sh
./1hour/bug_bts_5808/cases/bug_bts_5808.sh
./1hour/bug_bts_5775/cases/bug_bts_5775.sh
./1hour/bug_xdbms4341/cases/bug_xdbms4341.sh
./1hour/bug_bts_14432/cases/bug_bts_14432.sh
./1hour/bug_bts_5904/cases/bug_bts_5904.sh
./1hour/bug_bts_15419_3_asc/cases/bug_bts_15419_3_asc.sh
./1hour/bug_bts_6007/cases/bug_bts_6007.sh
./1hour/bug_bts_4931/cases/bug_bts_4931.sh
./1hour/bug_bts_8163/cases/bug_bts_8163.sh
./1hour/_03_new_feature_02/cases/_03_new_feature_02.sh
./1hour/bug_bts_5064/cases/bug_bts_5064.sh
./1hour/bug_bts_17984/cases/bug_bts_17984.sh
./1hour/bug_bts_7654/cases/bug_bts_7654.sh
./1hour/bug_bts_14765/cases/bug_bts_14765.sh
./1hour/bug_bts_8290/cases/bug_bts_8290.sh
./1hour/bug_bts_14767/cases/bug_bts_14767.sh
./1hour/bug_bts_6377/cases/bug_bts_6377.sh
./1hour/bug_bts_5824/cases/bug_bts_5824.sh
./1hour/bug_bts_16229/cases/bug_bts_16229.sh
./1hour/bug_bts_15247/cases/bug_bts_15247.sh
./1hour/bug_bts_8293/cases/bug_bts_8293.sh
./1hour/bug_bts_8148/cases/bug_bts_8148.sh
./_32_features_930/issue_11472_diagdb_show_index/_01_show_created_index/cases/_01_show_created_index.sh
./other/bug_xdbms_sus1194/cases/bug_xdbms_sus1194.sh
./other/bug_bts_17835/cases/bug_bts_17835.sh
./other/bug_bts_4523/cases/bug_bts_4523.sh
./other/bug_bts_17922/cases/bug_bts_17922.sh
./other/bug_bts_6981/cases/bug_bts_6981.sh
./other/bug_bts_14490/cases/bug_bts_14490.sh
./other/bug_bts_6667/cases/bug_bts_6667.sh
./other/bug_3627/cases/bug_3627.sh
./other/cubridsus1959/cases/cubridsus1959.sh
./other/bug_bts_17951/cases/bug_bts_17951.sh
./other/bug_bts_17967/cases/bug_bts_17967.sh
./other/bug_bts_17702/cases/bug_bts_17702.sh
./other/bug_bts_17675/cases/bug_bts_17675.sh
./3hour/bug_bts_13718/cases/bug_bts_13718.sh
./3hour/bug_bts_6635/cases/bug_bts_6635.sh
./3hour/bug_bts_16219/cases/bug_bts_16219.sh
./3hour/bug_bts_13592_1/cases/bug_bts_13592_1.sh
./3hour/bug_bts_14766/cases/bug_bts_14766.sh
./3hour/bug_bts_13592_2/cases/bug_bts_13592_2.sh
./_30_banana_qa/issue_9576/cases/issue_9576.sh
./_06_issues/_15_1h/bug_bts_14571/cases/bug_bts_14571.sh
./_06_issues/_11_2h/bug_bts_5936/cases/bug_bts_5936.sh
./_06_issues/_14_2h/bug_bts_13992/cases/bug_bts_13992.sh
./_06_issues/_14_1h/bug_bts_13186/cases/bug_bts_13186.sh
./_06_issues/_15_2h/bug_bts_17501/cases/bug_bts_17501.sh
./_04_misc/_05_query_cache/itrack_10020/cases/itrack_10020.sh
./2hour/_02_cursor_stress/cases/_02_cursor_stress.sh
./2hour/bug_bts_10009/cases/bug_bts_10009.sh
./2hour/bug_bts_9969/cases/bug_bts_9969.sh
```

