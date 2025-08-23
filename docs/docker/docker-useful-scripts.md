# Docker Useful Scrips

You can find some useful Docker scripts below.

## Image Copy (Tag)

You can copy an image from one Docker Hub account to another using the following script.

> You need to change the `OLD_IMAGE` and `NEW_IMAGE` variables accordingly.

<details>
<summary>copy_image.sh</summary>

```bash
#!/bin/bash

# Old image (source)
OLD_IMAGE="olduser/oldrepo:version"

# New image (destination)
NEW_IMAGE="newuser/newrepo:version"

# 1. Pull the old image
docker pull $OLD_IMAGE

# 2. Tag it with the new name
docker tag $OLD_IMAGE $NEW_IMAGE

# 3. Log in to Docker Hub (will ask for password unless already logged in)
#echo "Please log in to your Docker account:"
#docker login -u newuser

# 4. Push the new image
docker push $NEW_IMAGE

echo "✅ Done! $NEW_IMAGE is now available under your new account."

```

</details>

Usage:

```bash
chmod +x copy_image.sh
./copy_image.sh
```
