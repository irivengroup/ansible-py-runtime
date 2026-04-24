# PythonEnvironment

Ansible Role for Python Runtime Environment
Rôle Ansible robuste pour créer un virtualenv Python sur le control node Ansible.

## Objectifs

- Créer un virtualenv avec `python -m venv`
- Installer les prérequis système Python / pip / venv
- Installer les dépendances depuis `roles/PythonEnvironment/files/req.txt`
- Isoler les virtualenvs par version Python, nom logique et hash du requirements
- Éviter les conflits d'exécution concurrente avec `flock`
- Gérer les droits sous `/opt/PythonVirtualenvs`
- Produire des métadonnées et un freeze des dépendances installées

## Chemin généré

```text
/opt/PythonVirtualenvs/<py_ver>/<venv_name>-<req_hash>/.venv
```

Exemple :

```text
/opt/PythonVirtualenvs/3.12/myapp-a1b2c3d4e5f6/.venv
```

## Exemple

```bash
ansible-playbook -i localhost, -c local playbooks/setup.yml
```

Avec une autre version Python :

```bash
ansible-playbook -i localhost, -c local playbooks/setup.yml -e py_ver=3.11
```

Forcer la recréation :

```bash
ansible-playbook -i localhost, -c local playbooks/setup.yml -e venv_recreate=true
```


## Résolution du propriétaire local

Le rôle détermine automatiquement le propriétaire du virtualenv sur le control node avec l'ordre suivant :

1. `venv_owner` si explicitement fourni
2. `ansible_sudo_user`
3. `ansible_become_user`
4. `ansible_user`
5. variable d'environnement `SUDO_USER`
6. variable d'environnement `USER`

Le groupe suit `venv_group` si fourni, sinon `id -gn <owner>`.
