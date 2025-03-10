# API Reference

## Packages
- [batch.tensorstack.dev/v1beta1](#batchtensorstackdevv1beta1)


## batch.tensorstack.dev/v1beta1

Package v1beta1 contains API Schema definitions for the batch v1beta1 API group

### Resource Types
- [ColossalAIJob](#colossalaijob)
- [ColossalAIJobList](#colossalaijoblist)





#### ColossalAIJob



ColossalAIJob is the Schema for the colossalaijobs API

_Appears in:_
- [ColossalAIJobList](#colossalaijoblist)

| Field | Description |
| --- | --- |
| `apiVersion` _string_ | `batch.tensorstack.dev/v1beta1`
| `kind` _string_ | `ColossalAIJob`
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.24/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |
| `spec` _[ColossalAIJobSpec](#colossalaijobspec)_ |  |
| `status` _[ColossalAIJobStatus](#colossalaijobstatus)_ |  |


#### ColossalAIJobList



ColossalAIJobList contains a list of ColossalAIJob.



| Field | Description |
| --- | --- |
| `apiVersion` _string_ | `batch.tensorstack.dev/v1beta1`
| `kind` _string_ | `ColossalAIJobList`
| `metadata` _[ListMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.24/#listmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |
| `items` _[ColossalAIJob](#colossalaijob) array_ |  |


#### ColossalAIJobSpec



ColossalAIJobSpec defines the configurations of a ColossalAI training job.

_Appears in:_
- [ColossalAIJob](#colossalaijob)

| Field | Description |
| --- | --- |
| `ssh` _[SSHConfig](#sshconfig)_ | SSH configs. |
| `runMode` _[RunMode](#runmode)_ | The desired running mode of the job, defaults to `Immediate`. |
| `runPolicy` _[RunPolicy](#runpolicy)_ | Controls the handling of completed replicas and other related processes. |
| `scheduler` _SchedulePolicy_ | Specifies the scheduler to request for resources. Defaults to cluster default scheduler. |
| `torchConfig` _[TorchConfig](#torchconfig)_ | Describes how to start the colossalai job. |
| `replicaSpecs` _[ReplicaSpec](#replicaspec) array_ | List of replica specs belonging to the job. There must be at least one replica defined for a Job. |


#### ColossalAIJobStatus



ColossalAIJobStatus describes the observed state of ColossalAIJob.

_Appears in:_
- [ColossalAIJob](#colossalaijob)

| Field | Description |
| --- | --- |
| `tasks` _[Tasks](#tasks) array_ | The statuses of individual tasks. |
| `aggregate` _[Aggregate](#aggregate)_ | The number of replicas in each phase. |
| `phase` _JobPhase_ | Provides a simple, high-level summary of where the Job is in its lifecycle. Note that this is NOT indended to be a comprehensive state machine. |
| `conditions` _[JobCondition](#jobcondition) array_ | The latest available observations of an object's current state. |


#### ReplicaSpec



ReplicaSpec defines the desired state of replicas.

_Appears in:_
- [ColossalAIJobSpec](#colossalaijobspec)

| Field | Description |
| --- | --- |
| `type` _[ReplicaType](#replicatype)_ | Replica type. |
| `replicas` _integer_ | The desired number of replicas of this replica type. Defaults to 1. |
| `restartPolicy` _[RestartPolicy](#restartpolicy)_ | Restart policy for replicas of this replica type. One of Always, OnFailure, Never. Optional: Default to OnFailure. |
| `template` _[PodTemplateSpec](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.24/#podtemplatespec-v1-core)_ | Defines the template used to create pods. |


#### ReplicaType

_Underlying type:_ `string`



_Appears in:_
- [ReplicaSpec](#replicaspec)



#### RestartPolicy



RestartPolicy describes how the replica should be restarted.

_Appears in:_
- [ReplicaSpec](#replicaspec)

| Field | Description |
| --- | --- |
| `policy` _[RestartPolicyType](#restartpolicytype)_ | The policy to restart finished replica. |
| `limit` _integer_ | The maximum number of restarts. Optional: Default to 0. |


#### RestartPolicyType

_Underlying type:_ `string`



_Appears in:_
- [RestartPolicy](#restartpolicy)



#### RunPolicy



RunPolicy dictates specific actions to be taken by the controller upon job completion.

_Appears in:_
- [ColossalAIJobSpec](#colossalaijobspec)

| Field | Description |
| --- | --- |
| `cleanUpWorkers` _boolean_ | Defaults to false. |


#### SSHConfig



SSHConfig specifies various configurations for running the SSH daemon (sshd).

_Appears in:_
- [ColossalAIJobSpec](#colossalaijobspec)

| Field | Description |
| --- | --- |
| `authMountPath` _string_ | SSHAuthMountPath is the directory where SSH keys are mounted. Defaults to "/root/.ssh". |
| `sshdPath` _string_ | The location of the sshd executable file. |


#### TorchConfig



MPIConfig describes how to start the mpi job.

_Appears in:_
- [ColossalAIJobSpec](#colossalaijobspec)

| Field | Description |
| --- | --- |
| `procPerWorker` _integer_ | The number of processes of a worker. Defaults to 1. |
| `script` _string array_ | Specifies the command used to start the workers. |
| `extraArgs` _string array_ | Args of torchrun. |


