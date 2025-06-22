<template>
  <div class="issue-list-container">
    <h2 class="page-title">이슈 목록 페이지입니다.</h2>

    <button class="btn btn-primary new-issue-btn" @click="goToNewIssue">
      새 이슈 생성
    </button>

    <div class="filter-group">
      <label for="statusFilter" class="filter-label">상태 필터:</label>
      <select v-model="selectedStatus" id="statusFilter" class="filter-select">
        <option value="">전체</option>
        <option value="PENDING">대기 중</option>
        <option value="IN_PROGRESS">진행 중</option>
        <option value="COMPLETED">완료됨</option>
        <option value="CANCELLED">취소됨</option>
      </select>
    </div>

    <ul v-if="filteredIssues.length" class="issue-list">
      <li
        v-for="issue in filteredIssues"
        :key="issue.id"
        class="issue-item"
        @click="goToIssueDetail(issue.id)"
      >
        <h3 class="issue-title">{{ issue.title }}</h3>
        <p class="issue-info">상태: {{ statusLabel(issue.status) }}</p>
        <p class="issue-info">담당자: {{ issue.user ? issue.user.name : '담당자 없음' }}</p>
        <p class="issue-info">생성일: {{ formatDate(issue.createdAt) }}</p>
      </li>
    </ul>

    <p v-else class="no-issues-msg">선택한 상태에 해당하는 이슈가 없습니다.</p>
  </div>
</template>

<script>
import { issues } from '@/data/mockData'

export default {
  name: 'IssueList',
  data() {
    return {
      issues,
      selectedStatus: '',
    }
  },
  computed: {
    filteredIssues() {
      return this.selectedStatus
        ? this.issues.filter((issue) => issue.status === this.selectedStatus)
        : this.issues
    },
  },
  methods: {
    formatDate(isoString) {
      const options = { year: 'numeric', month: '2-digit', day: '2-digit' }
      return new Date(isoString).toLocaleDateString('ko-KR', options)
    },
    statusLabel(status) {
      const labels = {
        PENDING: '대기 중',
        IN_PROGRESS: '진행 중',
        COMPLETED: '완료됨',
        CANCELLED: '취소됨',
      }
      return labels[status] || '알 수 없음'
    },
    goToIssueDetail(id) {
      this.$router.push(`/issues/${id}`)
    },
    goToNewIssue() {
      this.$router.push('/issues/new')
    },
  },
}
</script>

<style scoped>
.issue-list-container {
  max-width: 700px;
  margin: 2rem auto;
  padding: 1rem 1.5rem;
  background: #f9fafb;
  border-radius: 8px;
  box-shadow: 0 1px 6px rgba(0, 0, 0, 0.1);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
    Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

.page-title {
  text-align: center;
  margin-bottom: 1.5rem;
  color: #222;
  font-weight: 600;
  font-size: 1.8rem;
}

.btn {
  cursor: pointer;
  padding: 0.5rem 1.2rem;
  font-weight: 600;
  border: none;
  border-radius: 5px;
  font-size: 1rem;
  transition: background-color 0.3s ease;
  color: white;
  user-select: none;
}

.btn-primary {
  background-color: #3b82f6;
}

.btn-primary:hover {
  background-color: #2563eb;
}

.new-issue-btn {
  display: block;
  margin: 0 auto 1rem auto;
}

.filter-group {
  margin-bottom: 1.5rem;
  display: flex;
  align-items: center;
  gap: 8px;
  justify-content: center;
}

.filter-label {
  font-weight: 500;
  color: #444;
}

.filter-select {
  padding: 0.4rem 0.8rem;
  font-size: 1rem;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-family: inherit;
  cursor: pointer;
  transition: border-color 0.3s ease;
}

.filter-select:hover,
.filter-select:focus {
  border-color: #3b82f6;
  outline: none;
  box-shadow: 0 0 5px rgba(59, 130, 246, 0.4);
}

.issue-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.issue-item {
  background: white;
  border: 1px solid #ddd;
  border-radius: 6px;
  padding: 1rem 1.2rem;
  margin-top: 0.75rem;
  cursor: pointer;
  transition: box-shadow 0.25s ease, border-color 0.25s ease;
}

.issue-item:hover {
  border-color: #3b82f6;
  box-shadow: 0 2px 10px rgba(59, 130, 246, 0.2);
}

.issue-title {
  margin: 0 0 0.5rem 0;
  color: #1e40af;
  font-weight: 700;
  font-size: 1.2rem;
}

.issue-info {
  margin: 0.2rem 0;
  color: #555;
  font-size: 0.95rem;
}

.no-issues-msg {
  text-align: center;
  color: #777;
  font-style: italic;
  margin-top: 2rem;
}
</style>
