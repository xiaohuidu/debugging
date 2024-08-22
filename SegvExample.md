**Segv report:**

```
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 1 of 7
Thread Restart 1 of 1
Single Thread Process Initialization 1 of 7
Multiple Thread Process Initialization 1 of 12
Perform Thread Restart
Function trace:
000000001196def7 0000000011a6d7ca 00000000108be59c 00000000108c47b0 00000000115fac90
00000000115fb352 0000000011606f5c 000000001156726c 
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2182"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1911"",""logs_sent"":""245""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 2 of 7
Register Dump:
     R8= 0000000000000000        R9= 00007fd9b67f2f18       R10= 0000000000000001
    R11= 0000000000000000       R12= 0000000000000000       R13= 0000000000000008
    R14= 0000000000000000       R15= 4cfe657a00f4000c       RDI= 0000000000000000
    RSI= 0000000000000005       RBP= 00007fd9b67f33e0       RBX= 0000000000000008
    RDX= 00007fd9b67f2f52       RAX= 0000000000000001       RCX= 0000000000000000
    RSP= 00007fd9b67f32c0       RIP= 000000001196def7       EFL= 0000000000010202
 CSGSFS= 002b000000000033       ERR= 0000000000000006    TRAPNO= 000000000000000e
OLDMASK= 0000000000004000       CR2= 0000000000000000   
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2219"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1912"",""logs_sent"":""246""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 3 of 7
Stack dump:
Stack pointer = 00007fd9b67f32c0
Stack dump:
<00007fd9b67f32c0> 6cb95f0b da7f0000 08000000 00000000
<00007fd9b67f32d0> 06000000 da7f0000 0c00f400 7a65fe4c
<00007fd9b67f32e0> 1b250800 00000000 0c00f400 7a65fe4c
<00007fd9b67f32f0> d0337fb6 d97f0500 1b250800 7a65fe4c
<00007fd9b67f3300> 01000000 00000000 01000000 06000000
<00007fd9b67f3310> 80337fb6 d97f0000 d2003710 00000000
<00007fd9b67f3320> 00000000 00000000 1b250800 ffff0000
<00007fd9b67f3330> 01000000 00000000 05000000 67413c93
<00007fd9b67f3340> 00000000 00000000 00000000 00000000
<00007fd9b67f3350> 01000000 00000000 0c00f400 7a65fe4c
<00007fd9b67f3360> d0337fb6 d97f0000 0c00f400 7a65fe4c
<00007fd9b67f3370> 5f000000 00000000 33000000 00000000
<00007fd9b67f3380> e0357fb6 d97f0000 193f3710 00000000
<00007fd9b67f3390> 0c00f400 7a65fe4c 00000000 00000000
<00007fd9b67f33a0> b0357fb6 d97f0000 cb7425dd db7f0000
<00007fd9b67f33b0> 0180adfb 3a61746c ccb95f0b da7f0000
<00007fd9b67f33c0> 6cb95f0b da7f0000 04000000 00000000
<00007fd9b67f33d0> 10000000 00000000 0c00f400 7a65fe4c
<00007fd9b67f33e0> e0357fb6 d97f0000 d2d7a611 00000000 // RBP:findMediaPolicyData4NK
<00007fd9b67f33f0> 00000000 00000000 00000000 00000000
<00007fd9b67f3400> 00000000 00000000 00000000 00000000
<00007fd9b67f3410> 00000000 00000000 33000000 00000000
<00007fd9b67f3420> 0c815f0b 00000000 08000000 d97f0000
<00007fd9b67f3430> d4b95f0b da7f0000 00000000 00000000
<00007fd9b67f3440> 0c845f0b da7f0000 b8a020e9 d97f0000
<00007fd9b67f3450> 2e000000 00000000 00000000 00000000
<00007fd9b67f3460> e0357fb6 d97f0000 5a5a6011 00000000
<00007fd9b67f3470> ffffffff d97f0000 b16bcee3 db7f0000
<00007fd9b67f3480> b0347fb6 d97f0000 40a45add db7f0000
<00007fd9b67f3490> 00000000 03000000 a04c0000 03000000
<00007fd9b67f34a0> 00000000 00000000 33000000 00000000
<00007fd9b67f34b0> e0357fb6 d97f0000 33000000 00000000
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2236"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1913"",""logs_sent"":""247""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 4 of 7
Stack dump:
Stack pointer = 00007fd9b67f34c0
Stack dump:
<00007fd9b67f34c0> 6cb95f0b da7f0000 179623dd db7f0000
<00007fd9b67f34d0> 401d0000 00000000 18000000 30000000
<00007fd9b67f34e0> b0357fb6 d97f0000 f0347fb6 d97f0000
<00007fd9b67f34f0> 1b000000 13000000 a04cb614 00000000
<00007fd9b67f3500> 00000000 00000000 333128dd db7f0000
<00007fd9b67f3510> 00000000 00000000 80975a14 00000000
<00007fd9b67f3520> 09000000 00000000 404c6012 00000000
<00007fd9b67f3530> 10d820e9 d97f0000 08815f0b da7f0000
<00007fd9b67f3540> 09000000 00000000 2c545f0b da7f0000
<00007fd9b67f3550> 24000000 00000000 6c875f0b da7f0000
<00007fd9b67f3560> 24000000 00000000 f8a020e9 d97f0000
<00007fd9b67f3570> 14000000 00000000 b7f298a0 3344b586
<00007fd9b67f3580> c0387fb6 d97f0000 1a000000 00000000
<00007fd9b67f3590> 1a000000 00000000 6c985f0b da7f0000
<00007fd9b67f35a0> 2c545f0b da7f0000 17351910 00000000
<00007fd9b67f35b0> 30300000 00000000 0ca45f0b da7f0000
<00007fd9b67f35c0> 6cb95f0b da7f0000 0c00f400 7a65fe4c //r13(*skey): 4cfe657a00f4000c. r12(*key): 00007fda0b5fb96c
<00007fd9b67f35d0> 5f000000 00000000 33000000 00000000 // r15(key_len): 0000000000000033, r14(data_type): 000000000000005f
<00007fd9b67f35e0> 60367fb6 d97f0000 a4e58b10 00000000 // RBP: MediaPolicy_nk_red_data_rcvCheckpt
<00007fd9b67f35f0> 5f000000 d97f0000 33000000 da7f0000 //5f000000 是入栈的data_type
<00007fd9b67f3600> 80975a14 00000000 0c00f400 7a65fe4c
<00007fd9b67f3610> 0c00f400 7a65fe4c 2c345f0b 10000000
<00007fd9b67f3620> ccb95f0b da7f0000 33000000 00000000
<00007fd9b67f3630> b0377fb6 d97f0000 0ca45f0b da7f0000
<00007fd9b67f3640> 09000000 00000000 08000000 00000000
<00007fd9b67f3650> c4387fb6 d97f0000 00477fb6 d97f0000
<00007fd9b67f3660> d0377fb6 d97f0000 b8478c10 00000000 // RBP: STIproc_pard_restore
<00007fd9b67f3670> b83821e9 d97f0000 3b95b600 00000000
<00007fd9b67f3680> c0367fb6 d97f0000 b16bcee3 db7f0000
<00007fd9b67f3690> c0367fb6 d97f0000 e050cee3 db7f0000
<00007fd9b67f36a0> 28000000 00000000 b8a020e9 d97f0000
<00007fd9b67f36b0> 28000000 00000000 2b000000 00000000
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2236"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1914"",""logs_sent"":""248""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 5 of 7
Stack dump:
Stack pointer = 00007fd9b67f36c0
Stack dump:
<00007fd9b67f36c0> c0387fb6 d97f0000 1a000000 00000000
<00007fd9b67f36d0> 1a000000 00000000 1a000000 00000000
<00007fd9b67f36e0> 90387fb6 d97f0000 76305611 00000000
<00007fd9b67f36f0> 70357fb6 d97f0000 f0c75212 00000000
<00007fd9b67f3700> 00000000 00000000 00000000 00000000
<00007fd9b67f3710> 01000000 da7f0000 00000000 00000000
<00007fd9b67f3720> 00000000 00000000 00000000 00000000
<00007fd9b67f3730> 2b000000 00000000 14000000 00000000
<00007fd9b67f3740> 10d820e9 d97f0000 a6385611 00000000
<00007fd9b67f3750> 00000000 00000000 2c855509 da7f0000
<00007fd9b67f3760> 2c0d5f0b da7f0000 ffefffff ffff0000
<00007fd9b67f3770> c4387fb6 d97f0000 00477fb6 d97f0000
<00007fd9b67f3780> a0377fb6 d97f0000 0ca45f0b da7f0000
<00007fd9b67f3790> 45000000 00000000 06000000 00000000
<00007fd9b67f37a0> 0851ee14 00000000 0ca45f0b da7f0000
<00007fd9b67f37b0> 30397fb6 d97f0000 ffefffff ffff0000
<00007fd9b67f37c0> c4387fb6 d97f0000 00477fb6 d97f0000
<00007fd9b67f37d0> f0377fb6 d97f0000 98ac5f11 00000000
<00007fd9b67f37e0> ffefffff ffff0000 c0387fb6 d97f0000
<00007fd9b67f37f0> 90387fb6 d97f0000 5ab35f11 00000000
<00007fd9b67f3800> 02000000 db7f0000 15913ee4 db7f0000
<00007fd9b67f3810> 15913ee4 db7f0000 19913ee4 04000000
<00007fd9b67f3820> 00000000 ffffffff 8c339907 da7f0000
<00007fd9b67f3830> ffffffff ffffffff 00000000 00000000
<00007fd9b67f3840> 00000000 00000000 00000000 00000000
<00007fd9b67f3850> 00000000 00000000 00000000 00000000
<00007fd9b67f3860> 00000000 00000000 c0387fb6 d97f0000
<00007fd9b67f3870> 30397fb6 d97f0000 c0387fb6 d97f0000
<00007fd9b67f3880> 30397fb6 d97f0000 ffefffff ffff0000
<00007fd9b67f3890> 803a7fb6 d97f0000 646f6011 00000000
<00007fd9b67f38a0> 00000000 00000000 00000000 00000000
<00007fd9b67f38b0> ffffffff 00000000 00000000 00000000
```
**Function Back Trace:**
```
Funct Addr   Funct Name                 Line #                          File
===============================================================================
000000001196def7     findMediaPolicyData4NK   1103      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_multiple_sessions_util.cpp
0000000011a6d7ca     MediaPolicy_nk_red_data_rcvCheckpt   1410      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
00000000108be59c     STIproc_pard_restore   158      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pard_resp.cpp
00000000108c47b0     STIproc_msg           784      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_msg.cpp
00000000115fac90     IMSmain               52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/main/linux_x86-64_csbc_ds_ims_proxy/../../../../../../../ssp/ds/ims/main/IMS_main.cpp
```
**源代码:**
```cpp
#pragma pack(1)
typedef struct {
        char *  key;
        uint32_t key_len;
        char *  data;
        uint32_t data_len;
        IMS_RED_DATA_TYPE  data_type;
        NK_SKEY  skey;
} APP_PARD_RESTORE_INFO;
#pragma pack()

 51 void 
 STIproc_pard_restore(IMS_MSG *msg_ptr)
 52 {
 53         APP_PARD_RESTORE_INFO *pard_info = &(msg_ptr->ims_hdr.msg_hdr_data.app_hdr_data.pard_restore_info);
 54
 55         /* Set local data from pard_info */
 56         if ( IS_VALID_PTR(pard_info) == FALSE )
 57         {
 58                 IMS_DLOG( IMS_LOGHIGH, "%s: Invalid pard restore info (%p).", __func__, pard_info);
 59                 return;
 60         }
 61         char * key = pard_info->key;
 62         if ( IS_VALID_PTR(key) == FALSE )
 63         {
 64                 IMS_DLOG( IMS_LOGHIGH, "%s: Invalid restore key (%p).", __func__, key);
 65                 return;
 66         }
 67         uint32_t key_len = pard_info->key_len;
 68         char *data = pard_info->data;
 69         if ( IS_VALID_PTR(data) == FALSE )
 70         {
 71                 IMS_DLOG( IMS_LOGHIGH, "%s: Invalid restore data (%p).", __func__, data);
 72                 return;
 73         }
 74         uint32_t data_len = pard_info->data_len;
 75         NK_SKEY  skey = pard_info->skey;
 76         IMS_RED_DATA_TYPE  data_type = pard_info->data_type;
 77         IMS_DLOG( IMS_LOGMED, "%s: restore data_type(%d). key_length (%d) key (%s) " PRT_SKEY_STR,
 78                  __func__, data_type, key_len, key_to_hex((char *)key, key_len), skey, skey);

...
	 MediaPolicy_nk_red_data_rcvCheckpt(key, key_len, data, data_len, data_type, skey)
}

void MediaPolicy_nk_red_data_rcvCheckpt(
        char *key,
        unsigned long key_len,
        char *data,
        unsigned long data_len,
        IMS_RED_DATA_TYPE data_type,
        NK_SKEY skey)


```
**汇编代码:**
```asm
   2 >>>>>>>> Disassembling function: **STIproc_pard_restore**(ims_MSG*)
   3 0x00000000108be270 <+0>:        55      push   %rbp
   4 0x00000000108be271 <+1>:        48 8d 97 0c f0 ff ff    lea    -0xff4(%rdi),%rdx // 计算APP_PARD_RESTORE_INFO *pard_info， 并放到rdx中（rdi指向传入参数IMS_MSG *msg_ptr）。
   5 0x00000000108be278 <+8>:        48 b8 ff ef ff ff ff ff 00 00   movabs $0xffffffffefff,%rax //把IS_VALID_PTR 的最大合法地址放到 rax
   6 0x00000000108be282 <+18>:       48 8d 4f 0c     lea    0xc(%rdi),%rcx
   7 0x00000000108be286 <+22>:       48 89 e5        mov    %rsp,%rbp //改变rbp 寄存器
   8 0x00000000108be289 <+25>:       41 57   push   %r15 //保存寄存器以防止覆盖丢失数据。
   9 0x00000000108be28b <+27>:       41 56   push   %r14 //保存寄存器以防止覆盖丢失数据。
  10 0x00000000108be28d <+29>:       41 55   push   %r13 //保存寄存器以防止覆盖丢失数据。
  11
  12 0x00000000108be270      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  13 0x00000000108be271      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  14 0x00000000108be278      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  15 0x00000000108be282      STIproc_pard_restore    53      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  16 0x00000000108be286      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  17 0x00000000108be289      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  18 0x00000000108be28b      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  19 0x00000000108be28d      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  20 ===================================
  21 0x00000000108be28f <+31>:       41 54   push   %r12 //保存寄存器以防止覆盖丢失数据。
  22 0x00000000108be291 <+33>:       53      push   %rbx //保存寄存器以防止覆盖丢失数据。
  23 0x00000000108be292 <+34>:       48 89 fb        mov    %rdi,%rbx // 把 IMS_MSG *msg_ptr放到rbx 寄存器
  24 0x00000000108be295 <+37>:       48 83 ec 48     sub    $0x48,%rsp // 为局部变量分配栈空间
  25 0x00000000108be299 <+41>:       48 39 c2        cmp    %rax,%rdx // if ( IS_VALID_PTR(pard_info) == FALSE )
  26 0x00000000108be29c <+44>:       0f 87 fe 00 00 00       ja     0x108be3a0 <STIproc_pard_restore(ims_MSG*)+304> // 跳转到0x108be3a0 打印log
  27 0x00000000108be2a2 <+50>:       4c 8b 67 0c     mov    0xc(%rdi),%r12 //r12存着 char *key
  28 0x00000000108be2a6 <+54>:       49 8d 94 24 00 f0 ff ff lea    -0x1000(%r12),%rdx // rdx 存储 char*key
  29
  30 0x00000000108be28f      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  31 0x00000000108be291      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  32 0x00000000108be292      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  33 0x00000000108be295      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  34 0x00000000108be299      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  35 0x00000000108be29c      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  36 0x00000000108be2a2      STIproc_pard_restore    61      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  37 0x00000000108be2a6      STIproc_pard_restore    62      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  38 ===================================
  39 0x00000000108be2ae <+62>:       48 39 c2        cmp    %rax,%rdx // if ( IS_VALID_PTR(key) == FALSE )
  40 0x00000000108be2b1 <+65>:       0f 87 09 01 00 00       ja     0x108be3c0 <STIproc_pard_restore(ims_MSG*)+336> //如果invalid， 跳转打log
  41 0x00000000108be2b7 <+71>:       4c 8b 57 18     mov    0x18(%rdi),%r10 
  42 0x00000000108be2bb <+75>:       44 8b 7f 14     mov    0x14(%rdi),%r15d
  43 0x00000000108be2bf <+79>:       49 8d 92 00 f0 ff ff    lea    -0x1000(%r10),%rdx // char *data 放到rdx
  44 0x00000000108be2c6 <+86>:       48 39 c2        cmp    %rax,%rdx // if ( IS_VALID_PTR(data) == FALSE )
  45 0x00000000108be2c9 <+89>:       0f 87 91 00 00 00       ja     0x108be360 <STIproc_pard_restore(ims_MSG*)+240> // 如果invliad， 跳转打印log
  46 0x00000000108be2cf <+95>:       4c 8b 6f 28     mov    0x28(%rdi),%r13 //NK_SKEY  skey， 后面放到了栈上
  47
  48 0x00000000108be2ae      STIproc_pard_restore    62      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  49 0x00000000108be2b1      STIproc_pard_restore    62      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  50 0x00000000108be2b7      STIproc_pard_restore    68      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  51 0x00000000108be2bb      STIproc_pard_restore    67      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  52 0x00000000108be2bf      STIproc_pard_restore    69      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  53 0x00000000108be2c6      STIproc_pard_restore    69      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  54 0x00000000108be2c9      STIproc_pard_restore    69      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  55 0x00000000108be2cf      STIproc_pard_restore    75      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  56 ===================================
  57 0x00000000108be2d3 <+99>:       8b 47 20        mov    0x20(%rdi),%eax // uint32_t data_len 拷贝到eax
  58 0x00000000108be2d6 <+102>:      45 89 fb        mov    %r15d,%r11d
  59 0x00000000108be2d9 <+105>:      44 8b 77 24     mov    0x24(%rdi),%r14d // data_type
  60 0x00000000108be2dd <+109>:      4c 89 de        mov    %r11,%rsi
  61 0x00000000108be2e0 <+112>:      4c 89 e7        mov    %r12,%rdi
  62 0x00000000108be2e3 <+115>:      4c 89 55 c0     mov    %r10,-0x40(%rbp)
  63 0x00000000108be2e7 <+119>:      4c 89 5d c8     mov    %r11,-0x38(%rbp)
  64 0x00000000108be2eb <+123>:      89 45 bc        mov    %eax,-0x44(%rbp)
  65
  66 0x00000000108be2d3      STIproc_pard_restore    74      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  67 0x00000000108be2d6      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  68 0x00000000108be2d9      STIproc_pard_restore    76      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  69 0x00000000108be2dd      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  70 0x00000000108be2e0      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  71 0x00000000108be2e3      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  72 0x00000000108be2e7      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  73 0x00000000108be2eb      STIproc_pard_restore    74      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  74 ===================================
  75 0x00000000108be2ee <+126>:      e8 bd 51 8d ff  callq  0x101934b0 <key_to_hex>
  76 0x00000000108be2f3 <+131>:      41 b9 40 4c 60 12       mov    $0x12604c40,%r9d
  77 0x00000000108be2f9 <+137>:      48 89 44 24 10  mov    %rax,0x10(%rsp)
  78 0x00000000108be2fe <+142>:      41 b8 e8 18 60 12       mov    $0x126018e8,%r8d
  79 0x00000000108be304 <+148>:      31 c0   xor    %eax,%eax
  80 0x00000000108be306 <+150>:      b9 4d 00 00 00  mov    $0x4d,%ecx
  81 0x00000000108be30b <+155>:      ba c3 09 60 12  mov    $0x126009c3,%edx
  82 0x00000000108be310 <+160>:      be 03 00 00 00  mov    $0x3,%esi
  83
  84 0x00000000108be2ee      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  85 0x00000000108be2f3      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  86 0x00000000108be2f9      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  87 0x00000000108be2fe      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  88 0x00000000108be304      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  89 0x00000000108be306      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  90 0x00000000108be30b      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  91 0x00000000108be310      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  92 ===================================
  93 0x00000000108be315 <+165>:      bf 04 00 00 00  mov    $0x4,%edi
  94 0x00000000108be31a <+170>:      4c 89 6c 24 20  mov    %r13,0x20(%rsp) //r13 存着char *skey
  95 0x00000000108be31f <+175>:      4c 89 6c 24 18  mov    %r13,0x18(%rsp)
  96 0x00000000108be324 <+180>:      44 89 7c 24 08  mov    %r15d,0x8(%rsp) //r15 存着 uint32_t key_len
  97 0x00000000108be329 <+185>:      44 89 34 24     mov    %r14d,(%rsp) //r14 低32 位入栈(data_type). 可以从STIproc_pard_restore的栈上对应的位置找到这个值(5f000000)
  98 0x00000000108be32d <+189>:      e8 2e 76 d4 00  callq  0x11605960 <IMS_dlog(IMS_MODULE_TYPE, IMS_LOG_LEVEL_TYPE, char cons     t*, int, char const*, ...)>
  99 0x00000000108be332 <+194>:      4c 89 ef        mov    %r13,%rdi
 100 0x00000000108be335 <+197>:      e8 56 5a ab ff  callq  0x10373d90 <LSkey_get_rcv_relinquishfrom_skey(NK_SKEY)>
 101
 102 0x00000000108be315      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 103 0x00000000108be31a      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 104 0x00000000108be31f      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 105 0x00000000108be324      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 106 0x00000000108be329      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 107 0x00000000108be32d      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 108 0x00000000108be332      STIproc_pard_restore    80      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 109 0x00000000108be335      STIproc_pard_restore    80      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
 110 ===================================

...
 440 0x00000000108be593 <+803>:      45 89 f0        mov    %r14d,%r8d //R8 存储第五个参数 data_type. R8 是从R14拷贝来的。

 454 0x00000000108be59f <+815>:      e8 6c ec 1a 01  callq  0x11a6d210 <MediaPolicy_nk_red_data_rcvCheckpt(char*, unsigned long     , char*, unsigned long, IMS_RED_DATA_TYPE, NK_SKEY)>

```

我们也可以从被调用函数 MediaPolicy_nk_red_data_rcvCheckpt() 中查看第五个参数r8 是不是被放入栈中了。

```asm
 603 >>>>>>>> Disassembling function: MediaPolicy_nk_red_data_rcvCheckpt(char*, unsigned long, char*, unsigned long, IMS_RED_DA     TA_TYPE, NK_SKEY)
 604 0x0000000011a6d210 <+0>:        55      push   %rbp
 605 0x0000000011a6d211 <+1>:        48 89 e5        mov    %rsp,%rbp
 606 0x0000000011a6d214 <+4>:        41 57   push   %r15 // r15存着uint32_t key_len
 607 0x0000000011a6d216 <+6>:        4d 89 cf        mov    %r9,%r15
 608 0x0000000011a6d219 <+9>:        41 56   push   %r14 // r14存着 unit32 data_type
 609 0x0000000011a6d21b <+11>:       49 89 ce        mov    %rcx,%r14
 610 0x0000000011a6d21e <+14>:       41 55   push   %r13 //r13存着char *skey
 611 0x0000000011a6d220 <+16>:       45 89 c5        mov    %r8d,%r13d //data_type, r8 被放入了r13
 612
 613 0x0000000011a6d210      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 614 0x0000000011a6d211      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 615 0x0000000011a6d214      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 616 0x0000000011a6d216      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 617 0x0000000011a6d219      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 618 0x0000000011a6d21b      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 619 0x0000000011a6d21e      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 620 0x0000000011a6d220      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 621 ===================================
 622 0x0000000011a6d223 <+19>:       41 54   push   %r12 // 12 存着 char *key
 623 0x0000000011a6d225 <+21>:       49 89 fc        mov    %rdi,%r12
 624 0x0000000011a6d228 <+24>:       53      push   %rbx
 625 0x0000000011a6d229 <+25>:       48 89 d3        mov    %rdx,%rbx
 626 0x0000000011a6d22c <+28>:       48 81 ec c8 01 00 00    sub    $0x1c8,%rsp
 627 0x0000000011a6d233 <+35>:       48 89 b5 38 fe ff ff    mov    %rsi,-0x1c8(%rbp)
 628 0x0000000011a6d23a <+42>:       e8 91 12 e4 ff  callq  0x118ae4d0 <ALGLogCheck()>
 629 0x0000000011a6d23f <+47>:       4d 85 e4        test   %r12,%r12
 630
 631 0x0000000011a6d223      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 632 0x0000000011a6d225      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 633 0x0000000011a6d228      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 634 0x0000000011a6d229      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 635 0x0000000011a6d22c      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 636 0x0000000011a6d233      MediaPolicy_nk_red_data_rcvCheckpt      1155    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 637 0x0000000011a6d23a      MediaPolicy_nk_red_data_rcvCheckpt      1157    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 638 0x0000000011a6d23f      MediaPolicy_nk_red_data_rcvCheckpt      1160    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 639 ===================================
 640 0x0000000011a6d242 <+50>:       0f 84 7d 00 00 00       je     0x11a6d2c5 <MediaPolicy_nk_red_data_rcvCheckpt(char*, unsig     ned long, char*, unsigned long, IMS_RED_DATA_TYPE, NK_SKEY)+181>
 641 0x0000000011a6d248 <+56>:       4d 85 f6        test   %r14,%r14
 642 0x0000000011a6d24b <+59>:       75 73   jne    0x11a6d2c0 <MediaPolicy_nk_red_data_rcvCheckpt(char*, unsigned long, char*,      unsigned long, IMS_RED_DATA_TYPE, NK_SKEY)+176>
 643 0x0000000011a6d24d <+61>:       80 3d dc 09 79 01 05    cmpb   $0x5,0x17909dc(%rip)        # 0x131fdc30 <ALG_log_level>
 644 0x0000000011a6d254 <+68>:       74 3a   je     0x11a6d290 <MediaPolicy_nk_red_data_rcvCheckpt(char*, unsigned long, char*,      unsigned long, IMS_RED_DATA_TYPE, NK_SKEY)+128>
 645 0x0000000011a6d256 <+70>:       bf 20 00 00 00  mov    $0x20,%edi
 646 0x0000000011a6d25b <+75>:       e8 f0 82 b9 ff  callq  0x11605550 <IMSPerCallLoggingGetSBCPerCallFlag(IMS_MODULE_TYPE)>
 647 0x0000000011a6d260 <+80>:       66 83 f8 01     cmp    $0x1,%ax
 648
 649 0x0000000011a6d242      MediaPolicy_nk_red_data_rcvCheckpt      1160    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 650 0x0000000011a6d248      MediaPolicy_nk_red_data_rcvCheckpt      1160 (discriminator 1)  /home/ngl/22.5.0-pp1/R3722.5.20230     717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.     cpp
 651 0x0000000011a6d24b      MediaPolicy_nk_red_data_rcvCheckpt      1160 (discriminator 1)  /home/ngl/22.5.0-pp1/R3722.5.20230     717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.     cpp
 652 0x0000000011a6d24d      MediaPolicy_nk_red_data_rcvCheckpt      1168    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 653 0x0000000011a6d254      MediaPolicy_nk_red_data_rcvCheckpt      1168    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 654 0x0000000011a6d256      MediaPolicy_nk_red_data_rcvCheckpt      1168 (discriminator 2)  /home/ngl/22.5.0-pp1/R3722.5.20230     717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.     cpp
 655 0x0000000011a6d25b      MediaPolicy_nk_red_data_rcvCheckpt      1168 (discriminator 2)  /home/ngl/22.5.0-pp1/R3722.5.20230     717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.     cpp
 656 0x0000000011a6d260      MediaPolicy_nk_red_data_rcvCheckpt      1168 (discriminator 2)  /home/ngl/22.5.0-pp1/R3722.5.20230     717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.     cpp
 657 ===================================
 658 0x0000000011a6d264 <+84>:       74 2a   je     0x11a6d290 <MediaPolicy_nk_red_data_rcvCheckpt(char*, unsigned long, char*,      unsigned long, IMS_RED_DATA_TYPE, NK_SKEY)+128>
 659 0x0000000011a6d266 <+86>:       41 8d 45 c7     lea    -0x39(%r13),%eax
 660 0x0000000011a6d26a <+90>:       48 89 9d 50 fe ff ff    mov    %rbx,-0x1b0(%rbp)
 661 0x0000000011a6d271 <+97>:       c7 85 48 fe ff ff 00 00 00 00   movl   $0x0,-0x1b8(%rbp)
 662 0x0000000011a6d27b <+107>:      83 f8 44        cmp    $0x44,%eax
 663 0x0000000011a6d27e <+110>:      0f 87 ad 04 00 00       ja     0x11a6d731 <MediaPolicy_nk_red_data_rcvCheckpt(char*, unsig     ned long, char*, unsigned long, IMS_RED_DATA_TYPE, NK_SKEY)+1313>
 664 0x0000000011a6d284 <+116>:      ff 24 c5 70 d1 8c 12    jmpq   *0x128cd170(,%rax,8)
 665 0x0000000011a6d28b <+123>:      0f 1f 44 00 00  nopl   0x0(%rax,%rax,1)
 666
 667 0x0000000011a6d264      MediaPolicy_nk_red_data_rcvCheckpt      1168 (discriminator 2)  /home/ngl/22.5.0-pp1/R3722.5.20230     717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.     cpp
 668 0x0000000011a6d266      MediaPolicy_nk_red_data_rcvCheckpt      1175    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 669 0x0000000011a6d26a      MediaPolicy_nk_red_data_rcvCheckpt      1170    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 670 0x0000000011a6d271      MediaPolicy_nk_red_data_rcvCheckpt      1171    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 671 0x0000000011a6d27b      MediaPolicy_nk_red_data_rcvCheckpt      1175    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 672 0x0000000011a6d27e      MediaPolicy_nk_red_data_rcvCheckpt      1175    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 673 0x0000000011a6d284      MediaPolicy_nk_red_data_rcvCheckpt      1175    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 674 0x0000000011a6d28b      MediaPolicy_nk_red_data_rcvCheckpt      1175    /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_     x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
 675 ===================================
 676 0x0000000011a6d290 <+128>:      4c 89 7c 24 08  mov    %r15,0x8(%rsp)
 677 0x0000000011a6d295 <+133>:      44 89 2c 24     mov    %r13d,(%rsp) // r13(data_type)被压栈了。
 678 0x0000000011a6d299 <+137>:      4d 89 e1        mov    %r12,%r9

```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY5MjAzMDg0Niw3MzA5OTgxMTZdfQ==
-->