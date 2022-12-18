# Jenkins

## Install Jenkins

```shell
sudo su

apt update && apt upgrade -y && apt install -y default-jre python3-venv

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | tee /etc/apt/sources.list.d/jenkins.list > /dev/null

apt update && apt install jenkins
```

## GitHub token

a50f3aaab32f44ff8ee2d52520ce2241

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
