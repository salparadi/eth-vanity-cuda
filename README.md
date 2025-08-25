# Vanity Eth Address
Vanity Eth Address is a tool to generate Ethereum addresses that match certain criteria, accelerated with NVIDIA CUDA-enabled GPUs.

This project is a CUDA-accelerated Ethereum vanity address generator written in C++. It utilizes NVIDIA GPUs to brute-force Ethereum addresses that match user-defined patterns, including both prefixes and suffixes. Designed for high performance, the tool can test billions of candidate keys per second, making it practical to generate customized Ethereum addresses that would be computationally expensive to obtain with CPU-only methods.

## Compilation

Set required PATH:
```
set path=C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.44.35207\bin\HostX86\x86;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\VC\VCPackages;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\CommonExtensions\Microsoft\TestWindow;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\bin\Roslyn;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Team Tools\DiagnosticsHub\Collector;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\Extensions\Microsoft\CodeCoverage.Console;C:\Program Files (x86)\Windows Kits\10\bin\10.0.26100.0\\x86;C:\Program Files (x86)\Windows Kits\10\bin\\x86;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\\MSBuild\Current\Bin\amd64;C:\Windows\Microsoft.NET\Framework\v4.0.30319;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\Tools\;%path%

set path=C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.44.35207\bin\Hostx86\x64;%path%
```

Compile with (on Windows):

```
nvcc src/main.cu -o eth-vanity-address.exe -std=c++17 -O3 -D_WIN64 -lbcrypt
```

## Usage
```
./eth-vanity-addresss [PARAMETERS]
    Scoring methods
      (-lz) --leading-zeros               Count zero bytes at the start of the address
       (-z) --zeros                       Count zero bytes anywhere in the address
    Modes (normal addresses by default)
       (-c) --contract                    Search for addresses and score the contract address generated using nonce=0
      (-c2) --contract2                   Search for contract addresses using the CREATE2 opcode
      (-c3) --contract3                   Search for contract addresses using a CREATE3 proxy deployer
    Other:
       (-d) --device <device_number>      Use device <device_number> (Add one for each device for multi-gpu)
       (-b) --bytecode <filename>         File containing contract bytecode (only needed when using --contract2 or --contract3)
       (-a) --address <address>           Sender contract address (only needed when using --contract2 or --contract3)
      (-ad) --deployer-address <address>  Deployer contract address (only needed when using --contract3)
       (-p) --prefix <hex>                Require address to start with <hex>
       (-s) --suffix <hex>                Require address to end with <hex>
       (-w) --work-scale <num>            Defaults to 15. Scales the work done in each kernel. If your GPU finishes kernels within a few seconds, you may benefit from increasing this number.

Examples:
    eth-vanity-address --zeros --device 0 --device 2 --work-scale 17
    eth-vanity-address --leading-zeros --contract2 --bytecode bytecode.txt --address 0x0000000000000000000000000000000000000000 --device 0
    eth-vanity-address --device 0 --prefix 51fA --suffix 38E115
```

## Benchmarks
| GPU  | Normal addresses | Contract addresses | CREATE2 addresses |
| ---- | ---------------- | ------------------ | ----------------- |
| 4090 | 3800M/s          | 2050M/s            | 4800M/s           |
| 3090 | 1600M/s          | 850M/s             | 2300M/s           |
| 3070 | 1000M/s          | 550M/s             | 1300M/s           |

Note that configuration and environment can affect performance.

## Requirements
* Visual Studio Build Tools 2022
* A NVIDIA CUDA-enabled GPU with a compute capability of at least 5.2 (Roughly anything above a GeForce GTX 950. For a full list [see here](https://developer.nvidia.com/cuda-gpus)).
