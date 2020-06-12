# .NET Analyzers Module

How to keep analyzer packages and ruleset as a Git submodule for a product that has a lot of repositories
and there is a need to keep analyzers configuration at the single place.

## Analyzers

Assume that [`analyzers`](analyzers) folder is a Git submodule of shared repository with analyzers configuration.
It contains 2 files:

- `General.ruleset` - provides a common set of code analysis rule sets. This is he single place to enable/disable rules globally.
- `Build.props` - contains shared code analysis project settings `<CodeAnalysisRuleSet>` and set of `<PackageReference>` items
  to add specific analysis NuGet packages that will be added to all projects.
  Its contents:
  ```xml
  <Project>
    <PropertyGroup>
      <CodeAnalysisRuleSet>$(SolutionDir)analyzers\General.ruleset</CodeAnalysisRuleSet>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.CodeAnalysis.FxCopAnalyzers" Version="3.0.0">
        <PrivateAssets>all</PrivateAssets>
        <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
      </PackageReference>
      <PackageReference Include="SonarAnalyzer.CSharp" Version="8.8.0.18411">
        <PrivateAssets>all</PrivateAssets>
        <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
      </PackageReference>
      <PackageReference Include="StyleCop.Analyzers" Version="1.1.118" />
    </ItemGroup>
  </Project>
  ```

In this project 3 packages are used: `Microsoft.CodeAnalysis.FxCopAnalyzers`, `SonarAnalyzer.CSharp` and `StyleCop.Analyzers`.

## Usage

1. Add a Git submodule with a path to `analyzers` folder.
2. For particular product repository add `Directory.Build.props` file into the root folder with contents:
   ```xml
   <Project>
     <Import Project="analyzers/Build.props"/>
   </Project>
   ```

That's all.

If you clone and open `SampleProduct.sln` you will find 3 analysis packages in each project.
After the solution build with code analysis several analysis warnings should be displayed.