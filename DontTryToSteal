#!/usr/bin/env bash
set -euo pipefail

HOSTNAME_SET=$(printf '%s' "R29ycmlsYUNvZGVyeg==" | base64 -d)
IMAGE="gorrilacoderz-vps"

show_banner() {
clear
printf "\e[38;5;196m==============================\e[0m\n"
printf "\e[38;5;226m   GORRILA CODERZ VPS MANAGER \e[0m\n"
printf "\e[38;5;46m==============================\e[0m\n"
echo "👍 Like  ⭐ Subscribe  🚀 Share"
echo
}

pause(){ read -r -p "Press Enter..." _; }

select_container() {
mapfile -t CONTAINERS < <(docker ps -a --filter label=gorrilacoderz-vps=true --format "{{.Names}}")
if [ ${#CONTAINERS[@]} -eq 0 ]; then
  echo "No VPS containers found."
  return 1
fi
for i in "${!CONTAINERS[@]}"; do
  echo "$((i+1))) ${CONTAINERS[$i]}"
done
read -r -p "Select: " idx
idx=$((idx-1))
SEL="${CONTAINERS[$idx]}"
}

build_image() {
mkdir -p /tmp/gorrilacoderz-vps
cd /tmp/gorrilacoderz-vps
cat > Dockerfile <<EOF
FROM ${BASE_IMAGE}
RUN apt update && apt install -y curl wget nano sudo bash git unzip zip tar vim htop screen tmux net-tools iproute2 procps docker.io python3 python3-pip nodejs npm && apt clean
CMD ["bash"]
EOF
if ! docker image inspect "$IMAGE" >/dev/null 2>&1; then
  printf "Installing "
  for i in $(seq 1 25); do printf "#"; sleep 0.08; done
  echo
  docker build -t "$IMAGE" . >/dev/null
fi
}

while true; do
show_banner
echo "1) Create VPS"
echo "2) List VPS"
echo "3) Open VPS"
echo "4) Stop VPS"
echo "5) Delete VPS"
echo "6) Exit"
read -r -p "Choice: " c
case "$c" in
1)
  echo "1) Ubuntu 24.04"
  echo "2) Debian 12"
  read -r -p "OS: " o
  if [ "$o" = "1" ]; then BASE_IMAGE=ubuntu:24.04; else BASE_IMAGE=debian:12; fi
  build_image
  NAME="gc-$(date +%s)"
  docker create -it \
    --label gorrilacoderz-vps=true \
    --name "$NAME" \
    --hostname "$HOSTNAME_SET" \
    "$IMAGE" bash >/dev/null
  echo "Created: $NAME"
  docker start -ai "$NAME"
  ;;
2)
  docker ps -a --filter label=gorrilacoderz-vps=true --format "Name: {{.Names}} | {{.Status}}"
  pause;;
3)
  select_container || { pause; continue; }
  docker start -ai "$SEL"
  ;;
4)
  select_container || { pause; continue; }
  docker stop "$SEL"
  pause;;
5)
  select_container || { pause; continue; }
  docker rm -f "$SEL"
  pause;;
6) exit 0;;
*) echo "Invalid"; sleep 1;;
esac
done
