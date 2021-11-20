<p class="has-line-data" data-line-start="0" data-line-end="1">Попробуйте запустить playbook на окружении из test.yml, зафиксируйте какое значение имеет факт some_fact для указанного хоста при выполнении playbook’a.</p>
<pre><code>ansible-playbook -i inventory/test.yml site.yml
    TASK [Print fact] ***************************
ok: [localhost] =&gt; {
    &quot;msg&quot;: 12
}
</code></pre>
<p class="has-line-data" data-line-start="8" data-line-end="9">Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на ‘all default fact’.</p>
<pre><code>playbook/group_vars/all/examp.yml
</code></pre>
<p class="has-line-data" data-line-start="12" data-line-end="13">Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.</p>
<pre><code>docker pull centos:centos7
docker pull ubuntu
docker run -it --name centos7 centos:centos7
docker run -it --name ubuntu ubuntu
</code></pre>
<p class="has-line-data" data-line-start="19" data-line-end="20">Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.</p>
<pre><code>mike@HOMEDX79SR:~/ans81/playbook$ docker ps
CONTAINER ID   IMAGE            COMMAND       CREATED          STATUS          PORTS     NAMES
9457b83a7ce2   centos:centos7   &quot;/bin/bash&quot;   21 seconds ago   Up 20 seconds             centos7
c96aeb84fd82   ubuntu           &quot;bash&quot;        21 minutes ago   Up 8 minutes              ubuntu


mike@HOMEDX79SR:~/ans81/playbook$ sudo ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] *********************

TASK [Gathering Facts] *******************************************
fatal: [ubuntu]: FAILED! =&gt; {&quot;ansible_facts&quot;: {}, &quot;changed&quot;: false, &quot;failed_modules&quot;: {&quot;ansible.legacy.setup&quot;: {&quot;ansible_facts&quot;: {&quot;discovered_interpreter_python&quot;: &quot;/usr/bin/python&quot;}, &quot;failed&quot;: true, &quot;module_stderr&quot;: &quot;/bin/sh: 1: /usr/bin/python: not found\n&quot;, &quot;module_stdout&quot;: &quot;&quot;, &quot;msg&quot;: &quot;The module failed to execute correctly, you probably need to set the interpreter.\nSee stdout/stderr for the exact error&quot;, &quot;rc&quot;: 127, &quot;warnings&quot;: [&quot;No python interpreters found for host ubuntu (tried ['/usr/bin/python', 'python3.9', 'python3.8', 'python3.7', 'python3.6', 'python3.5', 'python2.7', 'python2.6', '/usr/libexec/platform-python', '/usr/bin/python3', 'python'])&quot;]}}, &quot;msg&quot;: &quot;The following modules failed to execute: ansible.legacy.setup\n&quot;}
ok: [centos7]

TASK [Print OS] ********************
ok: [centos7] =&gt; {
    &quot;msg&quot;: &quot;CentOS&quot;
}

TASK [Print fact] ************************************
ok: [centos7] =&gt; {
    &quot;msg&quot;: &quot;el&quot;
}

PLAY RECAP ********************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
</code></pre>
<p class="has-line-data" data-line-start="49" data-line-end="51">Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для deb - ‘deb default fact’, для el - ‘el default fact’.<br>
Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.</p>
<pre><code>mike@HOMEDX79SR:~/ans81/playbook$ ansible-playbook -i inventory/prod.yml site.yml
PLAY [Print os facts] **********************
TASK [Gathering Facts] *******************************************
fatal: [ubuntu]: FAILED! =&gt; {&quot;ansible_facts&quot;: {}, &quot;changed&quot;: false, &quot;failed_modules&quot;: {&quot;ansible.legacy.setup&quot;: {&quot;ansible_facts&quot;: {&quot;discovered_interpreter_python&quot;: &quot;/usr/bin/python&quot;}, &quot;failed&quot;: true, &quot;module_stderr&quot;: &quot;/bin/sh: 1: /usr/bin/python: not found\n&quot;, &quot;module_stdout&quot;: &quot;&quot;, &quot;msg&quot;: &quot;The module failed to execute correctly, you probably need to set the interpreter.\nSee stdout/stderr for the exact error&quot;, &quot;rc&quot;: 127, &quot;warnings&quot;: [&quot;No python interpreters found for host ubuntu (tried ['/usr/bin/python', 'python3.9', 'python3.8', 'python3.7', 'python3.6', 'python3.5', 'python2.7', 'python2.6', '/usr/libexec/platform-python', '/usr/bin/python3', 'python'])&quot;]}}, &quot;msg&quot;: &quot;The following modules failed to execute: ansible.legacy.setup\n&quot;}
ok: [centos7]

TASK [Print OS] **********************
ok: [centos7] =&gt; {
    &quot;msg&quot;: &quot;CentOS&quot;
}

TASK [Print fact] ****************************************
ok: [centos7] =&gt; {
    &quot;msg&quot;: &quot;el default fact&quot;
}

PLAY RECAP ********************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
</code></pre>
<p class="has-line-data" data-line-start="72" data-line-end="73">При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.</p>
<pre><code>mike@HOMEDX79SR:~/ans81/playbook$ ansible-vault encrypt_string
New Vault password: 
Confirm New Vault password: 
Reading plaintext input from stdin. (ctrl-d to end input, twice if your content does not already have a newline)
&quot;el default fact&quot;
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          31303763653732663930356431643137326661313465643665393236636231636532656564396265
          3839633431623939643461323866666238623861343066610a613535646633633165313635643734
          31643566663031643130306639626438663431353936373139303462663236623061326366353061
          3361376134636362340a643133373930633232353661376166616331376566376231316130663861
          62626635393366383132313739653536653035623862616636393238353663303932
Encryption successful


mike@HOMEDX79SR:~/ans81/playbook$ ansible-vault encrypt_string
New Vault password: 
Confirm New Vault password: 
Reading plaintext input from stdin. (ctrl-d to end input, twice if your content does not already have a newline)
&quot;deb default fact&quot;
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          32613635353731336361326662616133373262356563313634396333653436326365363264643365
          3439373832373966323264333665323637393233313335610a343262303765643437663266356635
          35653063303862346665393266646562333834353061653139666237613030653066356232666530
          3838316164356336650a323863663239666439613364393136653738356361333436663537306265
          61633237393831666135376466326336383634656530326232383939306337376164
Encryption successful


 mike@HOMEDX79SR:~/ans81/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
 TASK [Print fact] ***********************************************************************************************************************************************************************************
ok: [centos7] =&gt; {
    &quot;msg&quot;: &quot;\&quot;el default fact\&quot;\n&quot;
}
</code></pre>
<p class="has-line-data" data-line-start="110" data-line-end="112">//<br>
В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.</p>
<pre><code>---
  el:
    hosts:
      centos7:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker
  local:
    hosts:
      localhost:
        ansible_connection: local
</code></pre>
<p class="has-line-data" data-line-start="127" data-line-end="128">Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что факты some_fact для каждого из хостов определены из верных group_vars.</p>
<pre><code>mike@HOMEDX79SR:~/ans81/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] *******************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************
ok: [localhost]
fatal: [ubuntu]: FAILED! =&gt; {&quot;ansible_facts&quot;: {}, &quot;changed&quot;: false, &quot;failed_modules&quot;: {&quot;ansible.legacy.setup&quot;: {&quot;ansible_facts&quot;: {&quot;discovered_interpreter_python&quot;: &quot;/usr/bin/python&quot;}, &quot;failed&quot;: true, &quot;module_stderr&quot;: &quot;/bin/sh: 1: /usr/bin/python: not found\n&quot;, &quot;module_stdout&quot;: &quot;&quot;, &quot;msg&quot;: &quot;The module failed to execute correctly, you probably need to set the interpreter.\nSee stdout/stderr for the exact error&quot;, &quot;rc&quot;: 127, &quot;warnings&quot;: [&quot;No python interpreters found for host ubuntu (tried ['/usr/bin/python', 'python3.9', 'python3.8', 'python3.7', 'python3.6', 'python3.5', 'python2.7', 'python2.6', '/usr/libexec/platform-python', '/usr/bin/python3', 'python'])&quot;]}}, &quot;msg&quot;: &quot;The following modules failed to execute: ansible.legacy.setup\n&quot;}
ok: [centos7]

TASK [Print OS] *************************************************************************************************************************************************************************************
ok: [localhost] =&gt; {
    &quot;msg&quot;: &quot;Ubuntu&quot;
}
ok: [centos7] =&gt; {
    &quot;msg&quot;: &quot;CentOS&quot;
}

TASK [Print fact] ***********************************************************************************************************************************************************************************
ok: [localhost] =&gt; {
    &quot;msg&quot;: &quot;all default fact&quot;
}
ok: [centos7] =&gt; {
    &quot;msg&quot;: &quot;\&quot;el default fact\&quot;\n&quot;
}

PLAY RECAP ******************************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
</code></pre>
