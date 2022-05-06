# vim

## Operation
- ctrl + n 补全 move down
- ctrl + p 补全 move up 

## Q & A
### 解决 swap file “*.swp”already exists！问题 
> 删除对应的 .swp 文件

## ideaVim config

```config
set number relativenumber
set idearefactormode=keep
set ideajoin
set surround
set easymotion

let mapleader = " "

noremap <Esc> <nop>
nmap <S-Enter> O<Esc>
nmap <CR> o<Esc>

nnoremap <C-j> :m +1<CR>
nnoremap <C-k> :m -2<CR>
inoremap <C-j> <Esc>:m +1<CR>gi
inoremap <C-k> <Esc>:m -2<CR>gi

" system clipboard
vmap <leader>y "+y
vmap <leader>d "+d
nmap <leader>y "+y
nmap <leader>p "+p
nmap <leader>P "+P
vmap <leader>p "+p
vmap <leader>P "+P
set clipboard+=unnamed

" scrolling
nmap <leader>d <C-d>
nmap <leader>u <C-u>
vmap <leader>d <C-d>
vmap <leader>u <C-u>

" actions
nmap <leader>h <action>(PreviousTab)
nmap <leader>l <action>(NextTab)
nmap <leader>bd <action>(CloseEditor)
nmap <leader>i <action>(Generate)
nmap <leader>m <action>(Git.Menu)
nmap <leader>s <action>(QuickChangeScheme)
nmap <leader>/ <action>(ShowErrorDescription)
nmap <leader>e <action>(GotoNextError)
nnoremap <leader><leader> <C-Tab>

set NERDTree
let g:NERDTreeMapActivateNode='l'
let g:NERDTreeMapJumpParent='h'
```