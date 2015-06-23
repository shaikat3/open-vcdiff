# open-vcdiff Development Guide #

If you are interested in understanding the internals of the open-vcdiff package,
building from source, or contributing ideas or modifications to the
project, then this document is for you.

## Introduction ##

First, let's give you some background of the project.

### Licensing ###

All open-vcdiff source and pre-built packages are provided under the
[Apache License](http://www.apache.org/licenses/LICENSE-2.0), Version 2.0.

The fine print: Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

### The open-vcdiff Community ###

The open-vcdiff community exists primarily through the [discussion group](http://groups.google.com/group/open-vcdiff), the
[issue tracker](http://code.google.com/p/open-vcdiff/issues/list) and, to a lesser extent, the [source control repository](http://code.google.com/p/open-vcdiff/source/checkout). You are definitely encouraged to contribute to the
discussion and you can also help us to keep the effectiveness of the
group high by following and promoting the guidelines listed here.

open-vcdiff is chiefly intended to support the SDCH protocol, so discussions in the
[SDCH](http://groups.google.com/group/SDCH) Google Group will often be of interest to
open-vcdiff developers.

#### Please Be Friendly ####

Showing courtesy and respect to others is a vital part of the Google
culture, and we strongly encourage everyone participating in
open-vcdiff development to join us in accepting nothing less. Of course,
being courteous is not the same as failing to constructively disagree
with each other, but it does mean that we should be respectful of each
other when enumerating the 42 technical reasons that a particular
proposal may not be the best choice. There's never a reason to be
antagonistic or dismissive toward anyone who is sincerely trying to
contribute to a discussion.

Sure, C++ development is serious business and all that, but it's also
a lot of fun. Let's keep it that way. Let's strive to be among the
friendliest communities in all of open source.

#### Where to Discuss open-vcdiff ####

As always, discuss open-vcdiff in the official
[open-vcdiff discussion group](http://groups.google.com/group/open-vcdiff)
on Google Groups. You don't have to actually submit
code in order to sign up. Your participation itself is a valuable
contribution.

## Working with the Code ##

If you want to get your hands dirty with the code inside open-vcdiff,
this is the section for you.

### Checking Out the Source from Subversion ###

Checking out the open-vcdiff source is most useful if you plan to
tweak it yourself.  You check out the source for open-vcdiff using a
[Subversion](http://subversion.tigris.org/) client as you would for any
other project hosted on Google Code.  Please see the instruction on
the [source code access page](http://code.google.com/p/open-vcdiff/source/checkout)
for how to do it.

### Compiling from Source ###

Once you check out the code, you can find basic instructions on how to compile it
in the [README](http://code.google.com/p/open-vcdiff/source/browse/trunk/README) file.
More detailed information is available in the on-line document HowToUseOpenVcdiff.

### Testing ###

We require thorough unit testing of the open-vcdiff package.
open-vcdiff is a low-level library that may be linked with any number of client and
server packages, and it is our responsibility as developers to provide reasonable
assurance that doing so will not break those packages.

Tests should be written for any new code, and changes should be
verified to not break existing tests before they are submitted for
review. To perform the tests, follow the instructions in [README](http://code.google.com/p/open-vcdiff/source/browse/trunk/README) and
verify that there are no failures.

## Contributing Code ##

We are excited that open-vcdiff is open source, and hope to get
great patches from the community. Before you fire up your favorite IDE
and begin hammering away at that new feature, though, please take the
time to read this section and understand the process. While it seems
rigorous, we want to keep a high standard of quality in the code
base.

### Contributor License Agreements ###

You must sign a Contributor License Agreement (CLA) before we can
accept any code.  The CLA protects you and us.

  * If you are an individual writing original source code and you're sure you own the intellectual property, then you'll need to sign an [individual CLA](http://code.google.com/legal/individual-cla-v1.0.html).
  * If you work for a company that wants to allow you to contribute your work to open-vcdiff, then you'll need to sign a [corporate CLA](http://code.google.com/legal/corporate-cla-v1.0.html).

Follow either of the two links above to access the appropriate CLA and
instructions for how to sign and return it.

### Coding Style ###

To keep the source consistent, readable, diffable and easy to merge,
we use a fairly rigid coding style, as defined by the [google-styleguide](http://code.google.com/p/google-styleguide/) project.  All patches will be expected
to conform to the style outlined [here](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml).

### Submitting Patches ###

Please do submit code. Here's what you need to do:

  1. Decide which code you want to submit. A submission should be a set of changes that addresses one issue in the [open-vcdiff issue tracker](http://code.google.com/p/open-vcdiff/issues/list). Please don't mix more than one logical change per submittal, because it makes the history hard to follow. If you want to make a change that doesn't have a corresponding issue in the issue tracker, please create one.
  1. Also, coordinate with team members that are listed on the issue in question. This ensures that work isn't being duplicated and communicating your plan early also generally leads to better patches.
  1. Ensure that your code adheres to  [Google C++ style guidelines](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml).
  1. Ensure that there are unit tests for your code.  (See [Testing](#Testing.md), above.)
  1. Sign a Contributor License Agreement.
  1. Make sure there is an issue in the issue tracker that your change addresses.  If necessary, create a new issue.
  1. Create a patch file using `svn diff`.
  1. We use [Rietveld](http://codereview.appspot.com/) to do web-based code reviews.  You can read about the tool [here](http://code.google.com/p/rietveld/wiki/CodeReviewHelp).  When you are ready, upload your patch via Rietveld and notify `open-vcdiff@googlegroups.com` to review it.  There are several ways to upload the patch.  We recommend using the [upload.py](http://codereview.appspot.com/static/upload.py) script.

### open-vcdiff Committers ###

The current members of the open-vcdiff engineering team are the only
committers at present. To ensure the quality and consistency of this
package, we require each new open-vcdiff team
member to earn the right to become a committer by following the
procedures in this document, writing consistently great code, and
demonstrating repeatedly that he or she truly gets the zen of
open-vcdiff.

## Release Process ##

We follow the typical release process for Subversion-based projects:

  1. A release branch named `release-X.Y` is created.
  1. Bugs are fixed and features are added in trunk; those individual patches are merged into the release branch until it's stable.
  1. An individual point release (the `Z` in `X.Y.Z`) is made by creating a tag from the branch.
  1. Repeat steps 2 and 3 throughout one release cycle (as determined by features or time).
  1. Go back to step 1 to create another release branch and so on.


---

Portions of this page are reproduced from the
[Google Test Development Guide](http://code.google.com/p/googletest/wiki/GoogleTestDevGuide), created and shared by Google and used according to terms described in the
[Creative Commons Attribution 2.5 License](http://creativecommons.org/licenses/by/2.5/).

That page, in turn, is based on the [Making GWT Better](http://code.google.com/webtoolkit/makinggwtbetter.html) guide from the [Google Web Toolkit](http://code.google.com/webtoolkit/) project.

Except as otherwise [noted](http://code.google.com/policies.html#restrictions), the content of this page is licensed under the [Creative Commons Attribution 2.5 License](http://creativecommons.org/licenses/by/2.5/).