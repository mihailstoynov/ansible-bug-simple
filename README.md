## Variable propagation fails with `include_role`

POC of a bug in ansible 2.7.1

A variable in a role propagates in transitively included roles (`include_role`) only until level 4. After that the variable becomes undefined

After executing the included playbook, role4 displays the default value propageted from role2, instead of the variable value "inherited" from role1 

Expected behaviour: execution of role4's debug message should display `value` instead of `role2 default`
```
$ ansible-playbook playbook.yml
[...]

TASK [role1 : debug] ******************************************************************************************************************************************************************
ok: [127.0.0.1] => {
    "msg": "var: [value]"
}

TASK [include_role : role2] ***********************************************************************************************************************************************************

TASK [role2 : debug] ******************************************************************************************************************************************************************
ok: [127.0.0.1] => {
    "msg": "var: [value]"
}

TASK [include_role : role3] ***********************************************************************************************************************************************************

TASK [role3 : debug] ******************************************************************************************************************************************************************
ok: [127.0.0.1] => {
    "msg": "var: [value]"
}

TASK [include_role : role4] ***********************************************************************************************************************************************************

TASK [role4 : debug] ******************************************************************************************************************************************************************
ok: [127.0.0.1] => {
    "msg": "var: [role2 default]"
}
```

## It works as intended if:

- the variable is in the playbook
- the variable in in `hosts_vars` or `group_vars`
- if the playbook uses `import_role`
- if the playbook uses `roles:` instead of `tasks:`