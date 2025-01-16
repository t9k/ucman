# API Reference

## Packages
- [batch.tensorstack.dev/v1beta1](#batchtensorstackdevv1beta1)


## batch.tensorstack.dev/v1beta1

Package v1beta1 contains API Schema definitions for the batch v1beta1 API group

### Resource Types
- [MPIJob](#mpijob)
- [MPIJobList](#mpijoblist)





#### MPIConfig



MPIConfig describes how to start the mpi job.

_Appears in:_
- [MPIJobSpec](#mpijobspec)

| Field | Description |
| --- | --- |
| `mca` _object (keys:string, values:string)_ | Open MPI uses Modular Component Architecture (MCA) parameters to provide a way to tune your runtime environment. |
| `home` _string_ | Open MPI installation path. |
| `extraArgs` _string array_ | Extra args for mpirun. |
| `script` _string array_ | Command line to start the MPI programs inside a worker pod. This is invoked by the launcher after all the worker pods have been created and entered ready state. |


#### MPIJob



MPIJob is the Schema for the mpijobs API

_Appears in:_
- [MPIJobList](#mpijoblist)

| Field | Description |
| --- | --- |
| `apiVersion` _string_ | `batch.tensorstack.dev/v1beta1`
| `kind` _string_ | `MPIJob`
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.24/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |
| `spec` _[MPIJobSpec](#mpijobspec)_ |  |
| `status` _[MPIJobStatus](#mpijobstatus)_ |  |


#### MPIJobList



MPIJobList contains a list of MPIJob



| Field | Description |
| --- | --- |
| `apiVersion` _string_ | `batch.tensorstack.dev/v1beta1`
| `kind` _string_ | `MPIJobList`
| `metadata` _[ListMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.24/#listmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |
| `items` _[MPIJob](#mpijob) array_ |  |


#### MPIJobSpec



MPIJobSpec outlines the intended configuration and execution parameters for a MPIJob.

_Appears in:_
- [MPIJob](#mpijob)

| Field | Description |
| --- | --- |
| `ssh` _[SSHConfig](#sshconfig)_ | SSH configs. |
| `runPolicy` _[RunPolicy](#runpolicy)_ | Execution policy configurations governing the behavior of the MPI job. |
| `runMode` _[RunMode](#runmode)_ | Job's execution behavior. If omitted, defaults to `Immediate` mode, and tasks are executed immediately upon submission. |
| `scheduler` _SchedulePolicy_ | Identifies the preferred scheduler for allocating resources to replicas. Defaults to cluster default scheduler. Use k8s default scheduler by default. |
| `mpiConfig` _[MPIConfig](#mpiconfig)_ | Describes how to start the mpi job. |
| `replicaSpecs` _[ReplicaSpec](#replicaspec) array_ | List of replica specs belonging to the job. There must be at least one replica defined for a Job. |


#### MPIJobStatus



MPIJobStatus represents the observed state of a MPIJob.

_Appears in:_
- [MPIJob](#mpijob)

| Field | Description |
| --- | --- |
| `tasks` _[Tasks](#tasks) array_ | Individual task status details of the job. |
| `aggregate` _[Aggregate](#aggregate)_ |  |
| `phase` _JobPhase_ | Provides a simple, high-level summary of where the Job is in its lifecycle. Note that this is NOT indended to be a comprehensive state machine. |
| `conditions` _[JobCondition](#jobcondition) array_ | The latest available observations of an object's current state. |


#### MPIReplicaType

_Underlying type:_ `string`



_Appears in:_
- [ReplicaSpec](#replicaspec)



#### ReplicaSpec



ReplicaSpec defines the desired state of replicas.

_Appears in:_
- [MPIJobSpec](#mpijobspec)

| Field | Description |
| --- | --- |
| `type` _[MPIReplicaType](#mpireplicatype)_ | Replica type. |
| `replicas` _integer_ | The desired number of replicas of this replica type. Defaults to 1. |
| `restartPolicy` _[RestartPolicy](#restartpolicy)_ | Restart policy for replicas of this replica type. One of Always, OnFailure, Never. Optional: Default to OnFailure. |
| `template` _[PodTemplateSpec](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.24/#podtemplatespec-v1-core)_ | Defines the template used to create pods. |


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



RunPolicy encapsulates various runtime policies of the MPI job, for example how to clean up resources.

_Appears in:_
- [MPIJobSpec](#mpijobspec)

| Field | Description |
| --- | --- |
| `cleanUpWorkers` _boolean_ | If worker replicas should be cleand up after they finish. Defaults false. |


#### SSHConfig



SSHConfig specifies various configurations for running the SSH daemon (sshd).

_Appears in:_
- [MPIJobSpec](#mpijobspec)

| Field | Description |
| --- | --- |
| `sshAuthMountPath` _string_ | SSHAuthMountPath is the directory where SSH keys are mounted. Defaults to "/root/.ssh". |
| `sshdPath` _string_ |  |


