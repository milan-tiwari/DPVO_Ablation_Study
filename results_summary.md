# Results Summary

## Baseline
The official DPVO baseline was reproduced successfully on the TartanAir mono test split.

## ConvLSTM Ablation
A ConvLSTM-style temporal refinement variant was implemented and trained successfully with resumable checkpoints. However, benchmark evaluation revealed instability during trajectory reconstruction, including cyclic pose-chain failures.

## Softmax Aggregation Tuning
A soft attention temperature tuning variant was implemented and trained successfully. Training loss improved, but evaluation at tested checkpoints showed instability across benchmark scenes.

## Main Takeaway
The project successfully reproduced the official baseline and built a robust HPC training/evaluation workflow. The ablation studies highlighted that modifications which appear promising during training can still introduce severe test-time instability in monocular visual odometry.

