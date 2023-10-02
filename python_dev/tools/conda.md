```
activate切换到所要导出的环境下:

conda env export > environment.yml
conda list --explicit > filename.txt
在导出文件目录下进入cmd界面，运行

conda env create -f environment.yml
conda install --name environment_Name --file filename.tx
```
