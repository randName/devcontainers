FROM ghcr.io/randname/node:latest AS noder

RUN node --version \
	&& npm install --global npm@10

FROM alpine:3.18

ARG USERNAME=devc

LABEL org.opencontainers.image.source=https://github.com/randName/devcontainers
LABEL org.opencontainers.image.description="Devcontainer for Node.js"
LABEL org.opencontainers.image.licenses=MIT

RUN apk add --no-cache \
		jq \
		git \
		zsh \
		tmux \
		curl \
		sudo \
		libstdc++

RUN addgroup -g 1000 $USERNAME \
	&& adduser -u 1000 -G $USERNAME -s /bin/zsh -D $USERNAME \
	&& echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME \
	&& chmod 0440 /etc/sudoers.d/$USERNAME

COPY --from=noder /usr/local/bin/ /usr/local/bin/
COPY --from=noder /usr/local/lib/ /usr/local/lib/
COPY --from=noder /usr/local/include/ /usr/local/include/

USER $USERNAME

WORKDIR /home/$USERNAME

RUN git clone --depth=1 \
	-c fsck.zeroPaddedFilemode=ignore \
	-c core.eol=lf -c core.autocrlf=false \
	-c fetch.fsck.zeroPaddedFilemode=ignore \
	-c receive.fsck.zeroPaddedFilemode=ignore \
	"https://github.com/ohmyzsh/ohmyzsh" .oh-my-zsh 2>&1 \
	&& cd .oh-my-zsh \
	&& git repack -a -d -f --depth=1 --window=1

RUN git clone --depth=1 --recursive \
	"https://github.com/randName/dotfiles" .dotfiles 2>&1 \
	&& ln -s /home/$USERNAME/.dotfiles/vimrc .vimrc \
	&& ln -s /home/$USERNAME/.dotfiles/zshrc .zshrc \
	&& ln -s /home/$USERNAME/.dotfiles/zshenv .zshenv \
	&& ln -s /home/$USERNAME/.dotfiles/tmux.conf .tmux.conf \
	&& cd .dotfiles \
	&& git repack -a -d -f --depth=1 --window=1

LABEL devcontainer.metadata='[{\
	"postCreateCommand": "sudo chown $(whoami) node_modules",\
	"mounts": ["type=volume,source=node_modules,target=${containerWorkspaceFolder}/node_modules"]\
}]'
