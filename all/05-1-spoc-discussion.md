#lec10 ���̣��̸߳���spoc��ϰ

NOTICE
- ��"w5l1"��ǵ���������Ҫ�ύ��ѧ�������ϵġ�
- ��"w5l1"��"spoc"��ǵ�����Ҫ�����廪ѧ�ֵ�ͬѧҪ��ʵ�������ɣ�����ʱ�ύ��ѧ����Ӧ��git repo�ϡ�
- ��"hard"��ǵ�����һ���Ѷȣ�����ʵ�֡�
- ��"easy"��ǵ��������ʵ�֣�����ʵ�֡�
- ��"midd"��ǵ�����һ��ˮƽ������ʵ�֡�

## ����˼����

### 11.1 ���̵ĸ���

- ʲô�ǽ��̣�ʲô�ǳ���
- ����ͽ�����ϵ��������ʲô��

### 11.2 ���̿��ƿ�

- ���̿��ƿ�Ĺ�����ʲô��
- ���̿��ƿ��а���ʲô��Ϣ��

### 11.3 ����״̬

- �������������е�����¼���Щʲô�����Ƕ�Ӧ�Ľ���״̬�仯��ʲô��
- �����л������еļ����ؼ��������
- ʱ���жϴ������Ⱥ���������
- ��ǰ���̵��ֳ��������
- �����л�����
��������һ�����н��̵��ֳ��ָ�

### 11.4 ��״̬����ģ��

- ���С������͵ȴ�����״̬�ĺ��壿
- ������4�����״̬ת�������״̬�޸Ĵ���

### 11.5 �������ģ��

- �������״̬��Ŀ����ʲô���ڴ��е�ʲô���ݷŵ�����У������ǹ���״̬��

### 11.6 �̵߳ĸ���

- �����̵߳�Ŀ����ʲô��ʲô���̣߳�
- �������̵߳���ϵ��������ʲô��

### 11.8 �ں��߳�

- �û��߳����ں��̵߳�������ʲô��

## SPOCС��˼����

(1) (spoc)���һ���򻯵Ľ��̹�����ϵͳ�����Թ����������¼򻯽���.������[�ο�����](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab4/process-concept-homework.py)���������룬����ɣ�YOUR CODE"���ֵ����ݣ����ɣ�����һ��

### ���̵�״̬ 

 - RUNNING - ��������ʹ��CPU
 - READY   - ���̿�ʹ��CPU
 - DONE    - ���̽���

### ���̵���Ϊ
 - ʹ��CPU, 
 - ����YIELD����,����ʹ��CPU


### ���̵���
 - ʹ��FIFO/FCFS�������ȷ���,
   - �Ȳ���λ��proc_info���е�curr_procԪ��(��ǰ����)֮��Ľ���(curr_proc+1..end)�Ƿ���READY̬��
   - �ٲ���λ��proc_info���е�curr_procԪ��(��ǰ����)֮ǰ�Ľ���(begin..curr_proc-1)�Ƿ���READY̬
   - �綼û�У�����ִ��curr_procֱ������

### �ؼ�ģ�����
 - ���̿��ƿ�
```
PROC_CODE = 'code_'
PROC_PC = 'pc_'
PROC_ID = 'pid_'
PROC_STATE = 'proc_state_'
```
 - ��ǰ���� curr_proc 
 - �����б�proc_info�Ǿ������̵Ķ��У�list����
 - �������У�������ʾ����Ҫ˵��ÿ���̵���Ϊ������������ʹ��CPU ;(2)�ȴ�I/O
```
   -l PROCESS_LIST, --processlist= X1:Y1,X2:Y2,...
   X �ǽ��̵�ִ��ָ����; 
   ����ִ��CPU�ı���(0..100) �������100����ʾ���ᷢ��yield����
```
 - �����л���Ϊ��ϵͳ������ʱ(when)�л�����:���̽�������̷���yield����

### ����ִ��
```
instruction_to_execute = self.proc_info[self.curr_proc][PROC_CODE].pop(0)
```

### �ؼ�����
 - ϵͳִ�й��̣�run
 - ִ��״̬�л�����:��move_to_ready/running/done��
 - ���Ⱥ�����next_proc

### ִ��ʵ��

#### ����
```
$./process-simulation.py -l 5:50
Process 0
  yld
  yld
  cpu
  cpu
  yld

Important behaviors:
  System will switch when the current process is FINISHED or ISSUES AN YIELD
Time     PID: 0 
  1     RUN:yld 
  2     RUN:yld 
  3     RUN:cpu 
  4     RUN:cpu 
  5     RUN:yld 

```

   
#### ����
```
$./process-simulation.py  -l 5:50,5:50
Produce a trace of what would happen when you run these processes:
Process 0
  yld
  yld
  cpu
  cpu
  yld

Process 1
  cpu
  yld
  cpu
  cpu
  yld

Important behaviors:
  System will switch when the current process is FINISHED or ISSUES AN YIELD
Time     PID: 0     PID: 1 
  1     RUN:yld      READY 
  2       READY    RUN:cpu 
  3       READY    RUN:yld 
  4     RUN:yld      READY 
  5       READY    RUN:cpu 
  6       READY    RUN:cpu 
  7       READY    RUN:yld 
  8     RUN:cpu      READY 
  9     RUN:cpu      READY 
 10     RUN:yld      READY 
 11     RUNNING       DONE 
```


```
#! /usr/bin/env python

import sys
from optparse import OptionParser
import random

# process states
STATE_RUNNING = 'RUNNING'
STATE_READY = 'READY'
STATE_DONE = 'DONE'

# members of process structure
PROC_CODE = 'code_'
PROC_PC = 'pc_'
PROC_ID = 'pid_'
PROC_STATE = 'proc_state_'

# things a process can do
DO_COMPUTE = 'cpu'
DO_YIELD = 'yld'


class scheduler:
    def __init__(self):
        # keep set of instructions for each of the processes
        self.proc_info = {}
        return

    def new_process(self):
        proc_id = len(self.proc_info)
        self.proc_info[proc_id] = {}
        self.proc_info[proc_id][PROC_PC] = 0
        self.proc_info[proc_id][PROC_ID] = proc_id
        self.proc_info[proc_id][PROC_CODE] = []
        self.proc_info[proc_id][PROC_STATE] = STATE_READY
        return proc_id

    def load(self, program_description):
        proc_id = self.new_process()
        tmp = program_description.split(':')
        if len(tmp) != 2:
            print 'Bad description (%s): Must be number <x:y>'
            print '  where X is the number of instructions'
            print '  and Y is the percent change that an instruction is CPU not YIELD'
            exit(1)

        num_instructions, chance_cpu = int(tmp[0]), float(tmp[1])/100.0
        for i in range(num_instructions):
            if random.random() < chance_cpu:
                self.proc_info[proc_id][PROC_CODE].append(DO_COMPUTE)
            else:
                self.proc_info[proc_id][PROC_CODE].append(DO_YIELD)
        return

    #change to READY STATE, the current proc's state should be expected
    #if pid==-1, then pid=self.curr_proc
    def move_to_ready(self, expected, pid=-1):
        if self.proc_info[self.curr_proc][PROC_STATE] != expected:
            return 
        self.proc_info[self.curr_proc][PROC_STATE] = STATE_READY
        return

    #change to RUNNING STATE, the current proc's state should be expected
    def move_to_running(self, expected):
        if self.proc_info[self.curr_proc][PROC_STATE] != expected:
            return 
        self.proc_info[self.curr_proc][PROC_STATE] = STATE_RUNNING
        return

    #change to DONE STATE, the current proc's state should be expected
    def move_to_done(self, expected):
        if self.proc_info[self.curr_proc][PROC_STATE] != expected:
            return 
        self.proc_info[self.curr_proc][PROC_STATE] = STATE_DONE
        return

    #choose next proc using FIFO/FCFS scheduling, If pid==-1, then pid=self.curr_proc
    def next_proc(self, pid=-1):

        proc_id = len(self.proc_info)
        for i in range(self.curr_proc + 1, proc_id): 
            if self.proc_info[i][PROC_STATE] == STATE_READY:
                self.curr_proc = i
                self.move_to_running(STATE_READY)
                return 
        for i in range(0, self.curr_proc):
            if self.proc_info[i][PROC_STATE] == STATE_READY:
                self.curr_proc = i
                self.move_to_running(STATE_READY)
                return
        self.move_to_running(STATE_READY)
        return

    def get_num_processes(self):
        return len(self.proc_info)

    def get_num_instructions(self, pid):
        return len(self.proc_info[pid][PROC_CODE])

    def get_instruction(self, pid, index):
        return self.proc_info[pid][PROC_CODE][index]

    def get_num_active(self):
        num_active = 0
        for pid in range(len(self.proc_info)):
            if self.proc_info[pid][PROC_STATE] != STATE_DONE:
                num_active += 1
        return num_active

    def get_num_runnable(self):
        num_active = 0
        for pid in range(len(self.proc_info)):
            if self.proc_info[pid][PROC_STATE] == STATE_READY or \
                   self.proc_info[pid][PROC_STATE] == STATE_RUNNING:
                num_active += 1
        return num_active

    def space(self, num_columns):
        for i in range(num_columns):
            print '%10s' % ' ',

    def check_if_done(self):
        if len(self.proc_info[self.curr_proc][PROC_CODE]) == 0:
            if self.proc_info[self.curr_proc][PROC_STATE] == STATE_RUNNING:
                self.move_to_done(STATE_RUNNING)
                self.next_proc()
        return

    def run(self):
        clock_tick = 0

        if len(self.proc_info) == 0:
            return

        # make first one active
        self.curr_proc = 0
        self.move_to_running(STATE_READY)

        # OUTPUT: heade`[rs for each column
        print '%s' % 'Time', 
        for pid in range(len(self.proc_info)):
            print '%10s' % ('PID:%2d' % (pid)),

        print ''

        # init statistics
        cpu_busy = 0

        while self.get_num_active() > 0:

            clock_tick += 1
            
            # if current proc is RUNNING and has an instruction, execute it
            # statistics clock_tick
            instruction_to_execute = ''
            if self.proc_info[self.curr_proc][PROC_STATE] == STATE_RUNNING and \
                   len(self.proc_info[self.curr_proc][PROC_CODE]) > 0:
                instruction_to_execute = self.proc_info[self.curr_proc][PROC_CODE].pop(0)

            # OUTPUT: print what everyone is up to
            print '%3d ' % clock_tick,
            for pid in range(len(self.proc_info)):
                if pid == self.curr_proc and instruction_to_execute != '':
                    print '%10s' % ('RUN:'+instruction_to_execute),
                else:
                    print '%10s' % (self.proc_info[pid][PROC_STATE]),

            print ''

            # if this is an YIELD instruction, switch to ready state
            # and add an io completion in the future
            if instruction_to_execute == DO_YIELD:
                self.move_to_ready(STATE_RUNNING)
                self.next_proc()

            # ENDCASE: check if currently running thing is out of instructions
            self.check_if_done()
        return (clock_tick)
        
#
# PARSE ARGUMENTS
#

parser = OptionParser()
parser.add_option('-s', '--seed', default=0, help='the random seed', action='store', type='int', dest='seed')
parser.add_option('-l', '--processlist', default='',
                  help='a comma-separated list of processes to run, in the form X1:Y1,X2:Y2,... where X is the number of instructions that process should run, and Y the chances (from 0 to 100) that an instruction will use the CPU or issue an YIELD',
                  action='store', type='string', dest='process_list')
parser.add_option('-p', '--printstats', help='print statistics at end; only useful with -c flag (otherwise stats are not printed)', action='store_true', default=False, dest='print_stats')
(options, args) = parser.parse_args()

random.seed(options.seed)

s = scheduler()

# example process description (10:100,10:100)
for p in options.process_list.split(','):
    s.load(p)


print 'Produce a trace of what would happen when you run these processes:'
for pid in range(s.get_num_processes()):
    print 'Process %d' % pid
    for inst in range(s.get_num_instructions(pid)):
        print '  %s' % s.get_instruction(pid, inst)
    print ''
print 'Important behaviors:'
print '  System will switch when the current process is FINISHED or ISSUES AN YIELD'

(clock_tick) = s.run()

if options.print_stats:
    print ''
    print 'Stats: Total Time %d' % clock_tick
    print ''

```