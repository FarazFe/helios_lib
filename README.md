The Helios Lib
===============


This is [Helios-Server](https://github.com/benadida/helios-server) (Helios is an end-to-end verifiable voting system.) as library,

This helios_lib version is completely independent of Django.


Install
-------

    >>> pip install helios_lib


Test
----

    >>> pytest --fulltrace -s helios_lib/


Example
-------

```python
from helios_lib.models import HeliosElection, HeliosVoter
from helios_lib.config import ELGAMAL_PARAMS

# Create election
helios_election = HeliosElection()

# Add trustee
trustee_default = helios_election.generate_helios_trustee(ELGAMAL_PARAMS)
helios_election.trustees.append(trustee_default)

# Add questions
question = HeliosElection.create_question(answers_count=5, minimum=0, maximum=2, result_type='relative')
helios_election.questions = [question]

# Add voters
voters_count = 4
helios_election.voters = [HeliosVoter() for _ in range(voters_count)]

# Freeze the election
helios_election.freeze()

# Cast votes, Encrypt votes of voters on the helios_lib side
helios_election.voters[0].vote = helios_election.encrypt_ballot('[[0,4]]')
helios_election.voters[1].vote = helios_election.encrypt_ballot('[[0]]')
helios_election.voters[2].vote = helios_election.encrypt_ballot('[[1]]')
helios_election.voters[3].vote = helios_election.encrypt_ballot('[[1,4]]')

# Verify votes of voters
for v in helios_election.voters:
    v.vote.verify(helios_election)

# Tally election
helios_election.num_cast_votes = 4
helios_election.compute_tally(helios_election.voters)
helios_trustee = helios_election.get_helios_trustee()
helios_election.helios_trustee_decrypt(helios_trustee)
helios_election.combine_decryptions()

# Result of election
assert helios_election.result == [[2, 2, 0, 0, 2]]

```



For more complex example refer to tests