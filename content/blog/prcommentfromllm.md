---
title: Como hacer que una LLM escriba la descripcion de tu PR por ti
description: Como hacer que una LLM escriba la descripcion de tu PR por ti
date: 2025-10-03
tags: LLM, Pull Request
---

Seguramemte haya mil formas de conseguir que una LLM cualquiera escriba la descripción de una pull request.
La forma que propongo es probablemente la más ubicua.

Una vez que hayas terminado de implementar los cambios y añadir o arreglar los tests podemos guardar en un fichero el diff de git usando este commando:

```sh
git diff origin/main..HEAD > changes.patch
```

Una vez guardado el diff ya podemos decirle a nuestra LLM de confianza, con acceso al código que nos genere la descripción de la pull request

```
Generate a clear and concise Pull Request description summarizing the changes introduced in this branch.
Use the diff provided in @changes.patch as the source of truth. Your description should include:
A high-level overview of what was changed and why
Any important implementation details or noteworthy decisions
Impact or risks (e.g. breaking changes, migrations, performance considerations)
Testing or validation steps, if applicable
```
