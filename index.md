## Welcome to Learning Kubernetes

This is a stupid blog I created with stupid markdown file with stupid github pages to track down my stupid journey of learning kubernetes as a software engineer.

I will store commands and links here so i might need it later.

### Day 1

- Install [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- Command to create a simple deployment
  ```
  kubectl create deployment gp-awesome-nginx --image=nginx
  ```
- Command to view replicaset
  ```
  kubectl get replicaset
  ```

- Basically, Deployment manages a replicaset. Replicaset manages a Pod. Pod is an abstraction of a container.
- 

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/gauravpanta/learningkubernetes/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
