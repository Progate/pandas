# browser-python: クロスビルドでは的の numpy のヘッダーを使わなければならない。
# 素の道は `py` に numpy を import させて `np.get_include()` を聞くものだが、その `py` は
# ホストの python なので、返ってくるのはホスト（64 bit）の numpy の include である。
# `_numpyconfig.h` の NPY_SIZEOF_LONG などは環境ごとに違うので、そのまま使うと
# 「コンパイルは通るのに配列の中身がずれる」形で壊れる。
incdir_numpy = get_option('numpy-include')
if incdir_numpy == ''
    incdir_numpy = run_command(
        py,
        [
            '-c',
            '''
import os
import numpy as np
try:
    # Check if include directory is inside the pandas dir
    # e.g. a venv created inside the pandas dir
    # If so, convert it to a relative path
    incdir = os.path.relpath(np.get_include())
except Exception:
    incdir = np.get_include()
print(incdir)
     ''',
        ],
        check: true,
    ).stdout().strip()
endif

inc_np = include_directories(incdir_numpy)
inc_pd = include_directories('_libs/include')

fs.copyfile('__init__.py')

subdir('_libs')

subdirs_list = [
    '_config',
    '_testing',
    'api',
    'arrays',
    'compat',
    'core',
    'errors',
    'io',
    'plotting',
    'tests',
    'tseries',
    'util',
]
foreach subdir : subdirs_list
    install_subdir(subdir, install_dir: py.get_install_dir() / 'pandas')
endforeach

top_level_py_list = [
    '__init__.py',
    '_typing.py',
    '_version.py',
    'conftest.py',
    'testing.py',
]
py.install_sources(top_level_py_list, subdir: 'pandas')
