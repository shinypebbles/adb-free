docker exec -u root --workdir / -ti adb-free /bin/sh
whoami

cd /etc/yum/vars
mv ociregion ociregion.sav
touch ociregion

dnf install vi -y
dnf install wget -y
dnf install ncurses -y
dnf install iputils -y
