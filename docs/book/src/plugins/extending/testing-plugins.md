# Write E2E Tests

You can check the [Kubebuilder/v4/test/e2e/utils][utils-kb] package, which offers `TestContext` with rich methods:

- [NewTestContext][new-context] helps define:
    - A temporary folder for testing projects.
    - A temporary controller-manager image.
    - The [Kubectl execution method][kubectl-ktc].
    - The CLI executable (whether `kubebuilder`, `operator-sdk`, or your extended CLI).

Once defined, you can use `TestContext` to:

1. **Setup the testing environment**, e.g.:
    - Clean up the environment and create a temporary directory. See [Prepare][prepare-method].
    - Install prerequisite CRDs. See [InstallCertManager][cert-manager-install], [InstallPrometheusManager][prometheus-manager-install].

2. **Validate the plugin behavior**, e.g.:
    - Trigger the plugin's bound subcommands. See [Init][init-subcommand], [CreateAPI][create-api-subcommand].
    - Use [PluginUtil][plugin-util] to verify scaffolded outputs. See [InsertCode][insert-code], [ReplaceInFile][replace-in-file], [UncommentCode][uncomment-code].

3. **Ensure the scaffolded output works**, e.g.:
    - Execute commands in your `Makefile`. See [Make][make-command].
    - Temporarily load an image of the testing controller. See [LoadImageToKindCluster][load-image-to-kind].
    - Call Kubectl to validate running resources. See [Kubectl][kubectl-ktc].

4. **Cleanup temporary resources after testing**:
    - Uninstall prerequisite CRDs. See [UninstallPrometheusOperManager][uninstall-prometheus-manager].
    - Delete the temporary directory. See [Destroy][destroy-method].

**References**:
- [operator-sdk e2e tests][sdk-e2e-tests]
- [kubebuilder e2e tests][kb-e2e-tests]


## Generate Test Samples

It's straightforward to view the content of sample projects generated
by your plugin.

For example, Kubebuilder generates [sample projects][kb-samples] based
on different plugins to validate the layouts.

You can also use `TestContext` to generate folders of scaffolded
projects from your plugin. The commands are similar to those
mentioned in [Extending CLI Features and Plugins][extending-cli].

Here’s a general workflow to create a sample project using the `go/v4` plugin (`kbc` is an instance of `TestContext`):

- **To initialize a project**:
  ```go
  By("initializing a project")
  err = kbc.Init(
  	"--plugins", "go/v4",
  	"--project-version", "3",
  	"--domain", kbc.Domain,
  	"--fetch-deps=false",
  )
  ExpectWithOffset(1, err).NotTo(HaveOccurred())
  ```

- **To define API:**
  ```go
  By("creating API definition")
  err = kbc.CreateAPI(
  	"--group", kbc.Group,
  	"--version", kbc.Version,
  	"--kind", kbc.Kind,
  	"--namespaced",
  	"--resource",
  	"--controller",
  	"--make=false",
  )
  ExpectWithOffset(1, err).NotTo(HaveOccurred())
  ```

- **To scaffold webhook configurations:**
  ```go
  By("scaffolding mutating and validating webhooks")
  err = kbc.CreateWebhook(
  	"--group", kbc.Group,
  	"--version", kbc.Version,
  	"--kind", kbc.Kind,
  	"--defaulting",
  	"--programmatic-validation",
  )
  ExpectWithOffset(1, err).NotTo(HaveOccurred())
  ```

[sdk-e2e-tests]: https://github.com/operator-framework/operator-sdk/tree/master/test/e2e/go
[new-context]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#NewTestContext
[kubectl-ktc]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#Kubectl
[prepare-method]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.Prepare
[cert-manager-install]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.InstallCertManager
[prometheus-manager-install]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.InstallPrometheusOperManager
[init-subcommand]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.Init
[create-api-subcommand]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.CreateAPI
[plugin-util]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/pkg/plugin/util
[insert-code]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/pkg/plugin/util#InsertCode
[replace-in-file]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/pkg/plugin/util#ReplaceInFile
[uncomment-code]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/pkg/plugin/util#UncommentCode
[make-command]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.Make
[load-image-to-kind]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.LoadImageToKindCluster
[uninstall-prometheus-manager]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.UninstallPrometheusOperManager
[destroy-method]: https://pkg.go.dev/sigs.k8s.io/kubebuilder/v4/test/e2e/utils#TestContext.Destroy
[kb-samples]: ./../../../../../testdata
[kb-e2e-tests]: ./../../../../../test/e2e/
[utils-kb]: ./../../../../../test/e2e/utils
[extending-cli]: ./extending_cli_features_and_plugins.md